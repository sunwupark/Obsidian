docker <- 유저스페이스 어플리케이션
kernel <- 서버사이드 도커엔진(containered, runc) 들이 명령을 받아서 설정을 해준다

편하게 쓸 수 있도록 해주는도구이다


컨테이너의 두가지 핵심기술
1. namespace로 유저스페이스 애플리케이션을 격리해준다!
- process
- network
- file system
- user
- uts(hostname)

남의 프로세스에 간섭을 주지 않도록 도와준다! => 샌드박스

안드로이드 -> 런타임을 도와주는 운영 환경이다!
- 밑단의 리눅스 커널에서 해주는 컨테이너 격리 기술

2. control group (cgroup)을 통해서 자원할당을 제한한다
- cpu, mem, disk, network등등 이런 자원 사용량을 제한하는 기능

================================================

도커 이미지를 만들어서 잘 관리해준다
- dockerhub

debian12 600MB
nginx(400MB) = RO(읽기 전용)

Mysql(1.2GB) = debian 12 600MB + mysql 600Mb

두개를 가져와서 사용한다고 할때 다 사용하는게 아니라 debian 은 공통으로 사용한다!

모든 공통된거를 재사용하게 만들어준다!

Union FileSystem

나의 이미지는 RO여야한다. 즉 Immutable해야한다
한번 만들어진 레이어는 불변한다

nginx1 + nginx2 + nginx3
/var/log 등등 변경되는 내용이 있다
RW layer추가로 만들어서..


3. 도커가 추가적으로 해주는 기능
layered filesystem = union filesystem을 통해서 .. 효율적으로 중복되는 파일 시스템을 잘 관리해준다!

========================================================

컨테이너 = 리눅스 커널 기술이다

docker build . -t myflask:1.0
docker build . -t myflask:1.1
docker build . -t myflask:1.2
docker build . -t myflask:1.3
docker tag myflask:1.3 myflask:latest
docker build . -t myflask:1.4
docker tag myflask:1.4 myflask:latest

latest는 옮겨 가지 않는다 고로 
docker tag myflask:1.4 myflask:latest
를 실행하여 수동으로 옮겨야한다

alpine 은 아무 것도 없는 알파인 리눅스를 기반
ap install, bash, ping 아무것도 없다
일반적으로 slim을 사용한다
- Debian 계열로 만들어진 가장 경량화된 놈이다!
- apt, bash 있다!

## 실습

간단한 서버를 띄워보자
```
import os
from flask import Flask, render_template
from werkzeug.middleware.proxy_fix import ProxyFix

app = Flask(__name__)
app.wsgi_app = ProxyFix(app.wsgi_app, x_proto=1, x_host=1)

color = os.environ.get('APP_COLOR')

@app.route('/')
def main():
	return render_template('hello.html', color=color)

if __name__ == "__main__":
	app.run(host='0.0.0.0', port=5001)
```

Dockerfile
```
FROM python:3.8-slim

WORKDIR /app
COPY . /app

RUN pip install -r requirements.txt
ENTRYPOINT ["python", "app.py"]
EXPOSE 5001
```

index.html
```
<!DOCTYPE html>
<title>Hello Flask</title>
<body style="background: {{color}}">
<div style="text-align: center;">
<h1>Hello, World!</h1>
</div>
</body>
</html>
```

1. 위의 프로젝트를 빌드한다
```
docker build . -t myflask:1.5
```

2. latest 태그로 설정한다
```
 docker tag myflask:1.5 myflask:latest
```

3. 색깔을 바꿔주며 실행한다
```
docker run -d -p 8000:5001 -e APP_COLOR=green --name myapp-green myflask
docker run -d -p 8001:5001 -e APP_COLOR=orange --name myapp-orange myflask
docker run -d -p 8002:5001 -e APP_COLOR=red --name myapp-red sunwupark/testing:latest
```

4. 화면을 확인한다 각 주소마다 색깔이 달라진다
![[Pasted image 20240622130653.png]]
![[Pasted image 20240622130701.png]]
![[Pasted image 20240622130710.png]]


nginx -> reverse proxy를 해보자!

docker run nginx하면 nginx를 다운받는다

nginx 컨테이너안의 /etc/nginx/con.f/default.conf를 설정하여 리버스 프록시를 할 수 있다

```
upstream my-apps {
server 127.0.0.1:8000;
server 127.0.0.1:8001 backup;
}

server{
listen 80 default_server;

locaiton / {
proxy_pass http://my-apps;
}
}
```

위와 같은 soma.conf를 작성한다음

```
docker run -d -p 80:80 -v /home/ubuntu/soma.conf:/etc/nginx/conf.d/default.conf --name myweb nginx
```

위의 명령어를 입력하여 실행한다 

80포트가 중복이므로 아래의 명령어를 실행

sudo systemctl stop nginx
sudo systemctl disable nginx

하지만 작동하지 않고 502 Bad Gateway가 나오는데 이는 갈곳이 없는 상태인 것이다.

그 이유는 현재 로컬호스트로 등록을 하였는데 이는 nginx내부의 컨테이너를 가리키는 말이기 때문입니다. 고로 아래의 도커 컨테이너의 네트워크 주소인

172.17.0.4
172.17.0.3
172.17.0.2

를 입력하여 설정한다면 잘 작동한다

하지만 언제나 매번 ip주소 찾아서 입력하기 싫으니

```
upstream my-apps {
        server myapp-green:5001;
        server myapp-red:5001 backup;
}

server{
    listen 80 default_server;

    location / {
        proxy_pass http://my-apps;
    }
```

위와 같이 이름을 등록하고 아래의 명령어로 설정한다

```
docker run -d -p 80:80 --link myapp-green:myapp-green --link myapp-red:myapp-red -v /home/ubuntu/soma.conf:/etc/nginx/conf.d/default.conf  --name myweb nginx
```

하지만 명령어가 너무 기니깐 이제는 docker-compose를 만들어서 작성하자

```
version: "3.0"

services:

web:

was:

db:

volumes:
# 위의 컨테이너에서 사용할 볼륨을 만들어준다

networks:
# 위의 컨테이너에서 사용할 네트워크를 만들어준다
```

위와 같은 형식으로 작성할 수 있다

```
version: "3.0"

services:
  my-web:
    image: nginx:latest
    ports:
    - 80:80
    volumes:
    - /home/ubuntu/soma.conf:/etc/nginx/conf.d/default.conf
    ## links:
    ## 선언을 안해도 자동적으로 도커 컴포즈는 연결을 해준다!
    ## - "myapp-green:myapp-green"

  myapp-green:
    image: sunwupark/testing:latest
    environment:
      - APP_COLOR=green
    ports:
      - 8001:5001

  myapp-orange:
    image: sunwupark/testing:latest
    environment:
      - APP_COLOR=orange
    ports:
      - 8002:5001


  myapp-red:
    image: sunwupark/testing:latest
    environment:
      - APP_COLOR=red
    ports:
      - 8003:5001
```

위와 같이 도커 컴포즈를 만들 수 있다

docker-compose up : 새롭게 만들어서 실행
docker-compose down: 종료 및 폐기함 (rm -f)
docker-compose stop: 멈춤 (docker stop)
docker-compose start: 재시작 (docker start)
docker-compose up -d

