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
- 