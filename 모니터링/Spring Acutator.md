## 얻을 수 있는 정보

**1. Health (/actuator/health):*
이 엔드포인트는 애플리케이션의 건강 상태를 보여줍니다. 데이터베이스 연결, 디스크 공간, 커스텀 체크 등 전반적인 시스템 건강을 확인할 수 있습니다.

**2. Info (/actuator/info):**
애플리케이션에 관한 일반적인 정보를 표시합니다. 이는 빌드 버전, 애플리케이션 이름 등의 정보를 포함할 수 있으며, application.properties 파일에서 설정할 수 있습니다.

**3. Metrics (/actuator/metrics):**
이 엔드포인트는 JVM, 메모리 사용, 스레드 상태, 가비지 컬렉터, HTTP 요청 등에 대한 메트릭스 정보를 제공합니다. 특정 메트릭에 대한 세부 정보도 조회할 수 있습니다.

**4. Env (/actuator/env):**
애플리케이션의 환경 설정 정보를 제공합니다. 활성 프로파일, 환경 변수, 애플리케이션 구성 등의 세부 정보를 볼 수 있습니다.

**5. ConfigProps (/actuator/configprops):**
애플리케이션에서 사용된 모든 @ConfigurationProperties 주석이 달린 클래스의 구성 속성 목록을 보여줍니다.

**6. Loggers (/actuator/loggers):**
애플리케이션의 로거 설정을 보여주고, 런타임에 로그 레벨을 변경할 수 있습니다.

**7. Thread Dump (/actuator/threaddump):**
애플리케이션의 현재 스레드 상태에 대한 자세한 덤프를 제공합니다. 이는 성능 문제 분석에 유용할 수 있습니다.

**8. Heap Dump (/actuator/heapdump):**
실시간으로 JVM 힙 덤프를 생성하여 다운로드합니다. 메모리 누수 분석 등에 사용될 수 있습니다.

**9. Auditevents (/actuator/auditevents):**
보안 및 유지 관리 관점에서 중요한 감사 이벤트 정보를 표시합니다.

**10. Mappings (/actuator/mappings):**
모든 @RequestMapping 경로와 그에 대응하는 핸들러를 보여줍니다.

**11. Scheduledtasks (/actuator/scheduledtasks):**
애플리케이션에서 구성된 모든 예약된 작업을 보여줍니다.



링크: https://hudi.blog/spring-boot-actuator-prometheus-grafana-set-up/

```groovy
implementation 'org.springframework.boot:spring-boot-starter-actuator'
runtimeOnly 'io.micrometer:micrometer-registry-prometheus'
```

## localhost:8080/actuator 접속시

{
  "_links": {
    "self": {
      "href": "http://localhost:8080/actuator",
      "templated": false
    },
    "prometheus": {
      "href": "http://localhost:8080/actuator/prometheus",
      "templated": false
    }
  }
}

우리는 이 엔드포인트 중 **Prometheus에서 사용되는 메트릭에 대한 엔드포인트를 노출**시켜야한다. 아래와 같이 **application.yml 파일**에 설정을 추가하자.

```yaml
management:
  endpoints:
    web:
      exposure:
        include: prometheus
```

![[Pasted image 20240618011416.png]]

## Prometheus Container를 띄우기 위해서

```
global:  
  scrape_interval: 15s  # How frequently to scrape targets by default.  
  
scrape_configs:  
  - job_name: prometheus  
    # Correct field to specify the endpoint to fetch metrics from targets.  
    metrics_path: '/actuator/prometheus'  
    static_configs:  
      - targets: ['host.docker.internal:8080']
```

다음과 같이 작성

#### 도커 컨테이너 실행
```bash
docker run \
    -d \
    -p 9090:9090 \
    -v <prometheus.yml 경로>:/etc/prometheus/prometheus.yml \
    prom/prometheus
```

![[Pasted image 20240618011608.png]]

다음과 같이 접속가능하다!

그런 다음 보기 위해 그라파나를 확인

```bash
docker run --name grafana -d -p 3000:3000 grafana/grafana
```

프로메테우스 설정 적용하기

![[Pasted image 20240618011657.png]]

여기서 -> 도커 컨테이너로 띄웠기 때문에 도커 컨테이너의 ip주소를 적어야한다!

```
docker inspect \
  -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' awesome_pare
```

=> 172.17.0.3

라는 결과가 나왔고 이를 입력하여 연결한다

다음 대시보드는

**‘[https://grafana.com/grafana/dashboards/4701-jvm-micrometer/’](https://grafana.com/grafana/dashboards/4701-jvm-micrometer/%E2%80%99)** 를 입력하고 **‘Load’** 를 클릭한다.

![[Pasted image 20240618011803.png]]


결과
![[Pasted image 20240618011825.png]]

이와 같은 결과가 나온다


질문1. 이 값을 어떻게 분석할 수 있는지 알아본다
질문2. 왜 도커 인터널에 접근이 불가능한지 알아보자!



