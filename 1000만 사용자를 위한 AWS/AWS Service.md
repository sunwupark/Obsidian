Scale-up/ Scale-down
	up: 4core 16gib, 16core 32gib으로 올린다
	down: 16core, 32gib => 8core, 16gib
Scale-in/Scale-out
	 out: instance 개수를 늘린다
	 in: instance 개수를 줄인다

### [[Load Balancer]]
[[NLB]]
- 인증서를 박을 수있다
- 여기서 암호화를 하고 NLB가 얘를 복호화시켜준다!
- 암호화에 대한 부담을 덜 수 있다!

[[ALB]](Application Load Balancer)
- 가용성 - Stickiness
	- 한번 들어온 인스턴스 대상
- Lambda를 대상으로 로드 밸런싱이 가능하다
- 컨텐츠 컨테이너 기반
- HTTP/2도 지원이 가능하다

ALB 종류
- external
	- 인터넷에 노출 될려면
- internal

cpu/ network를 분리하자!
![[스크린샷 2024-05-19 오전 9.40.14.png]]
정적 컨텐츠 뿐만 아니라 동적 컨텐츠도 비디어 스트리밍이면 바로 웹 인스턴스가 클라우드프론트에 보내면 사용자가 많아져도 인스턴스의 부하를 올리지 않고 클라우드프론트가 담당한다. 

매번 Database에 들어가면 부하가 되기 때문에 캐시를 사용해서 부하를 줄인다. ElastiCache Or DynamoDB

![[스크린샷 2024-05-19 오전 9.52.48.png]]
- 5분이상 cpu 50이상이면 트리거로 걸겠다
- 평소에는 한대, 피크때는 3대 뭐 그런식으로. [[오토스케일링]] 그룹을 지정할 . 수있다
- 컨테이너 이미지는 미리 만들어놓고
- 가장 기본이 되는게 클라우드 와치 => 모든 메트릭, 로그들을 모아주는 서비스
	- 얘를 기반으로 스케일링 된다!

모니터링: 클라우드 워치 - sns - 알람 가능
[[AWS X-Ray vs Cloud Watch]]

![[스크린샷 2024-05-19 오전 9.57.44.png]]

[[AWS Systems Manager]]
![[스크린샷 2024-05-19 오전 10.01.35.png]]
- 동시에 백대에 전달
- 모든 것을 전파하고 실행되고 s3에 다운되는 시간. 10분만에 대부분의 100대의 작업을 다 할 수 있다
- 아마존 서버에 접근할려면 바스티온 서버가 있어야하는데 => Systems Manager가 있다면 웹기반 안전하게 접근한다!!

Cloud9 - 동시 편집 다같이 visual studio code같은 - 웹기반이라 편하다
Code Commit - Github
CodeBuild - Github Action, Gitlab Runner(CI 기능)
CodeDeploy - 배포

![[스크린샷 2024-05-19 오전 10.20.30.png]]

![[스크린샷 2024-05-19 오전 10.22.40.png]]
- 데이터베이스 분할
- 폴리그랄?
- RDB하나에만 의존하지 않는다

[[Database Federation?]]
- 데이터베이스간 교차 쿼리에 어려움
- 샤딩
	- 계층의 복잡도 증가
	- 확장에 실질적인 제한 없음
	- 효율적인 구성을 위한 전문성
	- 함수나 . 키영역에 의한 샤딩
	- 마스터 디비가 추적 관리한다
