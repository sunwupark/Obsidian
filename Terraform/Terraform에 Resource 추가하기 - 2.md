```
createdFriend, err := r.client.CreateFriend([]hashicups.Friend{friend})
```

앞에서 만든 위와 같은 함수들은 결국 다 Product-API/ Upstream API에 존재합니다. 고로 각 API들을 연결지어주는 Client가 필요합니다 

1. 먼저 앞에서 필요하였던 Friend Type을 만들어줍니다
```
type Friend struct {
	ID          int                `json:"id"`
	Name        string             `json:"name"`
	Address     string             `json:"address"`
	Description string             `json:"description"`
	Image       string             `json:"image"`
}
```

2. go.mod 도 본인의 것으로 바꿔줍니다!
```
module github.com/sunwupark/hashicups-client-go
```

3. 마지막으로 HTTP 요청을 위한 CreateFriend
```
func (c *Client) CreateFriend(friends []Friend) (*Friend, error) {
	rb, err := json.Marshal(friends)
	if err != nil {
		return nil, err
	}

	req, err := http.NewRequest("POST", fmt.Sprintf("%s/friends", c.HostURL), strings.NewReader(string(rb)))
	if err != nil {
		return nil, err
	}

	body, err := c.doRequest(req, nil)
	if err != nil {
		return nil, err
	}

	friend := Friend{}
	err = json.Unmarshal(body, &friend)
	if err != nil {
		return nil, err
	}

	return &friend, nil
}
```

위와 같이 CreateFriend에 대한 요청을 보내게 됩니다! 
위에서 HostURL은 

![[Pasted image 20240623211646.png]]

이미 이렇게 정해져 있기 때문에 정해진 경로로 요청을 보내게 됩니다!

이렇게 HTTP요청을 보냈으니 Sunwu 회사는 이로직을 처리할 product-api를 만들어야합니다!

## Upstream-API

1. main.go 함수에서 라우팅을 수행합니다
```
// configure the telemetry
t := telemetry.New(conf.MetricsAddress)

// load the db connection
db, err := retryDBUntilReady()
if err != nil {
	logger.Error("Timeout waiting for database connection")
	os.Exit(1)
}

r := mux.NewRouter()
r.Use(hckit.TracingMiddleware)

// Enable CORS for all hosts
r.Use(cors.New(cors.Options{
	AllowedOrigins: []string{"*"},
	AllowedMethods: []string{"POST", "GET", "OPTIONS", "PUT", "DELETE"},
	AllowedHeaders: []string{"Accept", "content-type", "Content-Length", "Accept-Encoding", "X-CSRF-Token", "Authorization"},
}).Handler)

authMiddleware := handlers.NewAuthMiddleware(db, logger)

friendHandler := handlers.NewFriend(db, logger)
r.Handle("/friends", friendHandler).Methods("GET")
r.Handle("/friends/{id:[0-9]+}", friendHandler).Methods("GET")
r.HandleFunc("/friends", friendHandler.CreateFriend).Methods("POST")
r.HandleFunc("/friends/{id:[0-9]+}",friendHandler.UpdateFriend).Methods("PUT")
r.HandleFunc("/friends/{id:[0-9]+}",friendHandler.DeleteFriend).Methods("DELETE")

```


2. 각 요청에 대해서 처리하는 함수를 만든다 (friendhandler이다
```
// Createfriend creates a new friend
func (c *Friend) CreateFriend(rw http.ResponseWriter, r *http.Request) {
	c.log.Info("Handle friend | CreateFriend")

	var friends []model.Friend

	// 요청 본문을 읽고 출력합니다.
	reqBody, _ := io.ReadAll(r.Body)
	c.log.Info("Request Body", "body", string(reqBody))
	r.Body = io.NopCloser(bytes.NewBuffer(reqBody)) // 요청 본문을 리셋합니다.

	err := json.NewDecoder(r.Body).Decode(&friends)
	if err != nil {
		c.log.Error("Unable to decode JSON", "error", err)
		http.Error(rw, "Unable to parse request body", http.StatusInternalServerError)
		return
	}

	c.log.Info("Decoded Body", "body", friends)

	// 단일 카페 객체로 처리
	friend := friends[0]

	createdFriend, err := c.con.CreateFriend(friend)
	if err != nil {
		c.log.Error("Unable to create new friend", "error", err)
		http.Error(rw, fmt.Sprintf("Unable to create new friend: %s", err.Error()), http.StatusInternalServerError)
		return
	}

	d, err := createdFriend.ToJSON()
	if err != nil {
		c.log.Error("Unable to convert friend to JSON", "error", err)
		http.Error(rw, "Unable to create new friend", http.StatusInternalServerError)
		return
	}
	c.log.Info("Sending d", "d", d)

	rw.Write(d)
}
```

#### 왜 각 핸들러에는 ServerHTTP함수가 존재할까?
ServeHTTP 메서드는 Go에서 표준 HTTP 서버를 만들기 위한 중심적인 역할을 합니다. HTTP 핸들러의 기본 인터페이스인 http.Handler는 단 하나의 메서드인 ServeHTTP를 요구합니다. 이 메서드는 모든 HTTP 요청을 처리하고, 요청에 따른 적절한 로직을 실행하여 응답을 반환하는 역할을 합니다.

위와 같이 요청을 처리한다! 그런데 여기서 con.CreateFriend를 호출하는데 

3.  connection.go에서 실제 sql문에 들어갈 query를 만들어준다
```
// CreateFriend creates a new Friend in the database
func (c *PostgresSQL) CreateFriend(friend model.Friend) (model.Friend, error) {
	m := model.Friend{}

	rows, err := c.db.NamedQuery(
		`INSERT INTO friends (name, address, description, image, created_at, updated_at) 
		VALUES(:name, :address, :description, :image, now(), now()) 
		RETURNING id;`, map[string]interface{}{
			"name":        friend.Name,
			"address":     friend.Address,
			"description": friend.Description,
			"image":       friend.Image,
		})
	if err != nil {
		return m, err
	}
	defer rows.Close()

	if rows.Next() {
		err := rows.StructScan(&m)
		if err != nil {
			return m, err
		}
	}

	return m, nil
}
```

4. products.sql에 저장될 엔티티 만들어준다
```
CREATE TABLE friends (
    id SERIAL PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    address VARCHAR(255) NOT NULL,
    description TEXT,
    image VARCHAR(255),
    created_at TIMESTAMP NOT NULL,
    updated_at TIMESTAMP NOT NULL
);
```

5. Product에서 실제 가지고 있을 모델을 만들어준다
```
package model

import (
	"database/sql"
	"encoding/json"
	"io"
)

type Friends []Friend

// FromJSON serializes data from json
func (c *Friends) FromJSON(data io.Reader) error {
	de := json.NewDecoder(data)
	return de.Decode(c)
}

// ToJSON converts the collection to json
func (c *Friends) ToJSON() ([]byte, error) {
	return json.Marshal(c)
}

// Coffee defines a coffee in the database
type Friend struct {
	ID          int            `db:"id" json:"id"`
	Name        string         `db:"name" json:"name"`
	Address     string         `db:"address" json:"address"`
	Description string         `db:"description" json:"description"`
	Image       string         `db:"image" json:"image"`
	CreatedAt   string         `db:"created_at" json:"-"`
	UpdatedAt   string         `db:"updated_at" json:"-"`
	DeletedAt   sql.NullString `db:"deleted_at" json:"-"`
}

func (c *Friend) FromJSON(data io.Reader) error {
	de := json.NewDecoder(data)
	return de.Decode(c)
}

// ToJSON converts the collection to json
func (c *Friend) ToJSON() ([]byte, error) {
	return json.Marshal(c)
}
 
```

위의 과정을 마치고 나서 database폴더에 있는 도커파일과 main.go고에 있는 dockerfile을 이용하여 도커허브에 푸쉬한다!

푸쉬를 한 상태에서 다시 처음 Provider로 돌아온다. 도커 콤포즈

![[Pasted image 20240623213507.png]]
도커 콤포즈가 있는데 이를 실행한다!

```
version: "3.7"
services:
  api:
    image: "sunwupark/product-api:v0.0.22"
    ports:
      - "19090:9090"
    volumes:
      - ./conf.json:/config/config.json
    environment:
      CONFIG_FILE: "/config/config.json"
    depends_on:
      - db
  db:
    image: "sunwupark/product-api-db:v0.0.22"
    ports:
      - "15432:5432"
    environment:
      POSTGRES_DB: "products"
      POSTGRES_USER: "postgres"
      POSTGRES_PASSWORD: "password"
```

아까 우리가 만들었던 product-api와 api-db이다!


그런 프로바이더의 main.go를 실행하면!
![[Pasted image 20240623213636.png]]

실행되지 않는다

그 이유는 테라폼 코어가 main.go 파일 단독으로 실행되는 것을 원하지 않고 테라폼 파일과 함께 작동하길 원하기 때문입니다. 

위의 이유가 궁금해서 뤼튼에게 물어봤는데
1. main.go와 같은 단일 파일로는 전체 인프라 구성을 표현하기 어렵워 테라폼 파일을 통해 인프라 전체를 선언적으로 정의할 수 있어, 인프라 코드의 통합성을 유지
2. 테라폼은 인프라 상태를 .tfstate 파일에 저장하여 관리합니다. main.go 파일만으로는 이러한 상태 관리 기능을 제공할 수 없습니다.

등의 이유가 나왔습니다

암튼 고로 전에 작성 했던 main.tf를 실행시켜보자

![[Pasted image 20240623213922.png]]
테라폼 provider에서 HashiCups API 클라이언트를 생성하는 과정에서 발생
API 클라이언트 생성 시 유효한 자격 증명이 제공되지 않음

고로 프로바이더 구성에 필요한 하시컵스 계정정보를 만들어준다!

![[Pasted image 20240623213948.png]]

```
curl -X POST localhost:19090/signup -d '{"username":"education", "password":"test123"}'
```
위의 명령어를 통해 회원가입을 먼저 진행을 해야한다!

그후 실행을 해보면!
![[Pasted image 20240623214044.png]]

잘 작성이 된다!

terraform apply를 하게 되면

```
{
  "version": 4,
  "terraform_version": "1.8.4",
  "serial": 3,
  "lineage": "8618e49d-f9ce-9b90-2eca-a9e40ba36359",
  "outputs": {},
  "resources": [
    {
      "mode": "managed",
      "type": "sunwupark_friend",
      "name": "friend",
      "provider": "provider[\"registry.terraform.io/study/sunwupark-ossca\"]",
      "instances": [
        {
          "status": "tainted",
          "schema_version": 0,
          "attributes": {
            "address": "Seoul",
            "description": "Hello, I'm sunwupark",
            "id": "6",
            "image": "https://avatars.githubusercontent.com/u/12345678?v=4",
            "name": "sunwupark"
          },
          "sensitive_attributes": []
        }
      ]
    }
  ],
  "check_results": null
}

```

이렇게 terraform state가 작성되는 것을 확인할 수 있다!!

다음과 같은 과정을 통하여 프로바이더를 만드는 것인 것 같다!
