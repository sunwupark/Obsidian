
1. docker-compose up 을 실행한다

```
cd 03-read-coffees/docker_compose
docker-compose up -d
```

2. signup

```
terraform-provider-hashicups [main] ⚡  curl localhost:19090/health/readyz
ok%                                                                                                                                             terraform-provider-hashicups [main] ⚡  curl -X POST localhost:19090/signup -d '{"username":"education", "password":"test123"}'
{"UserID":1,"Username":"education","token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE3MTkwNTU0NzEsInRva2VuX2lkIjoxLCJ1c2VyX2lkIjoxLCJ1c2VybmFtZSI6ImVkdWNhdGlvbiJ9.IECMHzqo6op9yuCSfKYDgYRipgslnYBDLK3SYtecD8g"}
```

3. Run
```
cd examples/coffees
terraform plan
```
![[Pasted image 20240621202807.png]]

4. tfstate 확인
![[Pasted image 20240621202903.png]]

5. terraform show
![[Pasted image 20240621202957.png]]

# Chapter 04 
"github.com/hashicorp/terraform-plugin-log/tflog"를 import하여 log tflog사용한다
```
tflog.Info(ctx, "Configuring HashiCups client")
// error, warn등 여러가지 로그 출려가능하다
```

다음과 같이 말이죠!

```
ctx = tflog.SetField(ctx, "hashicups_host", host)
ctx = tflog.SetField(ctx, "hashicups_username", username)
ctx = tflog.MaskFieldValuesWithFieldKeys(ctx, "hashicups_password")
tflog.Debug(ctx, "Creating HashiCups client")
```

![[Pasted image 20240621203411.png]]

```
TF_LOG=TRACE terraform plan
```
으로 TRACE가능하다

![[Pasted image 20240621203819.png]]
- tflog.Info(ctx, "Configuring HashiCups client")를 확인할 수 있다
- 

![[Pasted image 20240621203907.png]]
- 로그 txt파일 만들 수 있따

# Chapter 05 - Order Resource

```
type orderResourceModel struct {
	ID types.String `tfsdk:"id"`
	Items []orderItemModel `tfsdk:"items"`
	LastUpdated types.String `tfsdk:"last_updated"`
}
```

![[Pasted image 20240621204119.png]]
정의하고

```
// orderResource is the resource implementation.

type orderResource struct {

client *hashicups.Client

}
```

정의하고
Configure, Read, Delete 등을 등록해준다

```
var (
_ resource.Resource = &orderResource{}
_ resource.ResourceWithConfigure = &orderResource{}
)
```

- 잘 구현했는지 확인

![[Pasted image 20240621204958.png]]
![[Pasted image 20240621205014.png]]

이미 클라우드에 생성된 정보를 테라폼으로 가져오는 import를 할 수 있따!

ImportState 인터페이스 메소드를 사용
어떤것을 기준으로 import? id를 기준으로 Import를 할 수 있다~

## Chapter 12 - TEST
![[Pasted image 20240621205343.png]]

![[Pasted image 20240621205638.png]]
테스트 실행

![[Pasted image 20240621205654.png]]
부분 함수 실행


구 SDK를 FW로 바꿔보자!
![[Pasted image 20240621213523.png]]

![[Pasted image 20240621213547.png]]
여기에 없는 것들을 해보자!!!

