- OSI Layer 7인 어플리케이션 계층에서 동작하는 로드밸런서

### 기능
- 리스너를 통해 RL, 호스트, 헤더, 메소드 등을 기반으로 요청의 규칙을 구성하여 처리할 수 있다. 
- 자동 스케일 업 + 스케일 다운
- 하나 이상의 타겟 그룹에 라우팅
- SSL offloadiing
- Algorithm: Round Robin, ([[최소 미해결 요청 라우팅 알고리즘]] 지원)

### 리스너
- IF의 조건이 만족하면 THEN의 작업을 한다

##### IF

| **규칙**              | **설명**                       | **예시**                                                           |
| ------------------- | ---------------------------- | ---------------------------------------------------------------- |
| host header         | 요청의 호스트 이름을 기반으로 라우팅         | [ex1.com](http://example1.com/), [ex2.com](http://example2.com/) |
| http header         | 요청의 HTTP 헤더 기반으로 라우팅         | User-Agent: Chrome                                               |
| http request method | 요청의 HTTP 메소드 기반으로 라우팅        | GET, PUT                                                         |
| path pattern        | 요청 URL의 경로 패턴을 기반으로 라우팅      | /img/*                                                           |
| query string        | 쿼리 문자열의 키/값 페어 또는 값 기반으로 라우팅 | ?name=product                                                    |
| source IP           | 요청의 소스 IP 주소 기반으로 라우팅        |                                                                  |
##### THEN

| **규칙**               | **설명**                                                                                                                                         |
| -------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| authenticate cognito | Amazon Cognito를 사용하여 [사용자 인증](https://docs.aws.amazon.com/ko_kr/elasticloadbalancing/latest/application/listener-authenticate-users.html)을 한다. |
| authenticate oidc    | OpenID Connect, 즉 OAuth 2.0 으로 사용자 인증을 한다.                                                                                                     |
| fixed response       | 사용자가 정의한 HTTP 응답을 반환한다.                                                                                                                        |
| forward              | 요청을 대상 그룹으로 전달한다.                                                                                                                              |
| redirect             | 요청을 다른 URL로 리다이렉션 한다.                                                                                                                          |

## Target Group
- 요청을 처리하는 대상의 기준
- health check

## [[Load Balancing vs Reverse Proxy]]
![[Pasted image 20240519214431.png]]