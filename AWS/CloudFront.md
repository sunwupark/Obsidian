- ﻿﻿Content Delivery Network (CDN)
- ﻿﻿Improves read performance, content is cached at the edge
- ﻿﻿Improves users experience
- ﻿﻿216 Point of Presence globally (edge locations)
- ﻿﻿DDoS protection (because worldwide), integration with Shield, AWS Web Application Firewall

**S3 버킷을 Origin으로 사용하기**

Amazon S3 버킷은 정적 파일(HTML, CSS, JS, 이미지 파일 등)을 저장하고 전 세계적으로 빠르게 제공하기 위해 CloudFront와 함께 사용할 수 있는 기본적인 Origin입니다. CloudFront는 이러한 파일을 캐시하여 전 세계의 edge 위치에서 사용자에게 더 빠르게 제공할 수 있습니다.

**2. 보안 강화: Origin Access Control (OAC)**
CloudFront는 보안을 강화하기 위해 Origin Access Control (OAC)을 제공합니다. 이는 이전의 Origin Access Identity (OAI)를 대체하는 새로운 방식으로, CloudFront가 S3 버킷에 안전하게 접근할 수 있도록 합니다. 이를 통해 CloudFront를 통해서만 S3 버킷의 콘텐츠에 접근이 가능하게 설정할 수 있습니다.

**3. CloudFront를 통한 파일 업로드 (Ingress)**
CloudFront는 단순히 콘텐츠를 배포하는 것 뿐만 아니라, 사용자가 S3 버킷에 파일을 업로드할 수 있도록 설정할 수도 있습니다. 이 기능을 통해 사용자는 전 세계 어디서나 저렴한 비용으로 빠르게 파일을 업로드할 수 있습니다.

**4. Custom Origin**
CloudFront는 다양한 타입의 HTTP 기반 backends를 지원합니다. 예를 들어, Application Load Balancer (ALB)나 EC2 인스턴스, 혹은 다른 HTTP 서버가 이에 해당됩니다. 이러한 각각의 서비스는 CloudFront의 Custom Origin으로 설정하여 사용할 수 있습니다.

**5. S3 웹사이트를 Origin으로 사용하기**
S3 버킷을 정적 웹사이트로 설정하면 HTTP를 통해 접근이 가능합니다. CloudFront는 이러한 S3 웹사이트를 Origin으로 설정하여 정적 웹 콘텐츠를 더욱 효율적으로 전달할 수 있습니다.

**결론**
CloudFront와 S3를 연결하여 사용하는 것은 콘텐츠 전송 속도를 개선하고 보안을 강화할 수 있는 효과적인 방법입니다. 또한, 여러 Origin과의 호환성을 통해 다양한 애플리케이션과 서비스에서의 활용성을 높일 수 있습니다. CloudFront의 구성 옵션과 관리 도구를 통해 사용자의 요구에 맞춘 세밀한 설정이 가능합니다.
![[스크린샷 2024-06-14 오후 4.13.57.png]]

# S3 Transfer Acceleration
![[스크린샷 2024-06-14 오후 4.19.27.png]]

Test the tool at: https://s3-accelerate-speedtest.s3-accelerate.amazonaws.com/en/accelerate-speed-comparsion.html

# AWS Global Accelerator
  
AWS Global Accelerator는 AWS 글로벌 네트워크를 활용하여 전 세계적으로 애플리케이션의 가용성과 성능을 향상시킵니다. 주요 특징 및 기능은 다음과 같습니다:
• **성능 향상**: AWS 내부 네트워크를 사용하여 애플리케이션으로의 경로를 최적화하여 성능을 최대 60%까지 개선할 수 있습니다.
• **Anycast IP 주소**: 애플리케이션에 대해 두 개의 Anycast IP 주소가 생성됩니다. 이 설정을 통해 트래픽은 가장 가까운 엣지 위치에서 AWS 네트워크로 들어갈 수 있습니다.
• **트래픽 라우팅**: 트래픽은 먼저 AWS Edge 위치에서 받아진 후 AWS 글로벌 네트워크를 통해 애플리케이션으로 전달됩니다.
![[스크린샷 2024-06-14 오후 4.25.43.png]]
**AWS CloudFront와의 비교**

AWS Global Accelerator와 CloudFront 모두 AWS 글로벌 네트워크와 엣지 위치를 활용하지만, 그 목적과 기능이 다릅니다:

• **공통 특징**:
• 전 세계적으로 AWS 엣지 위치를 사용합니다.
• DDoS 보호를 위해 AWS Shield와 통합됩니다.

• **AWS CloudFront**:
• **유형**: 컨텐츠 전송 네트워크(CDN).
• **주요 기능**: 이미지 및 비디오와 같은 캐시 가능한 콘텐츠의 성능을 향상시키며, 지연 시간을 줄이기 위해 엣지 위치에서 콘텐츠를 제공합니다.

• **AWS Global Accelerator**:
• **캐싱 없음**: 콘텐츠를 캐싱하지 않고 엣지에서 패킷을 프록시 처리하는 기능을 합니다.
• **성능**: TCP 또는 UDP를 통한 애플리케이션의 성능을 향상시키며 다양한 애플리케이션에 적합합니다.
• **사용 사례**:
• 정적 IP 주소가 필요한 HTTP 애플리케이션에 이상적입니다.
• 빠르고 결정적인 지역 장애 조치가 필요한 애플리케이션에 적합합니다.

결론적으로, CloudFront는 전 세계 사용자에게 캐시 가능한 콘텐츠를 빠르게 전달하기 위해 최적화되어 있는 반면, Global Accelerator는 사용자 트래픽의 라우팅을 최적화하여 애플리케이션의 반응성과 관리성을 향상시키는 데 초점을 맞추고 있습니다. 이는 캐시할 수 없는 동적 콘텐츠에 특히 유용합니다.

![[스크린샷 2024-06-14 오후 4.25.49.png]]

AWS Outposts
- **서버 랙**: AWS Outposts는 AWS 클라우드와 동일한 인프라, 서비스, API 및 도구를 제공하는 서버 랙입니다. 이를 통해 사용자는 클라우드에서와 동일하게 자체적인 애플리케이션을 온프레미스에서 구축할 수 있습니다.
- **설치 및 관리**: AWS는 온프레미스 인프라 내에 Outposts 랙을 설치하고 관리합니다. 이를 통해 사용자는 온프레미스에서 AWS 서비스를 이용할 수 있게 됩니다.
- **보안 책임**: Outposts 랙의 물리적 보안은 사용자 책임 하에 있습니다.

Benefits:  
• Low-latency access to on-premises systems  
• Local data processing  
• Data residency  
• Easier migration from on-premises to the cloud • Fully managed service

### AWS WaveLength

WaveLength Zones는 통신 제공업체의 데이터센터 내부에 구축된 인프라 배치로, 5G 네트워크의 가장자리에 위치합니다. 여기에 대한 주요 정보와 특징을 한국어로 정리하겠습니다.

**WaveLength Zones 특징**
• **5G 네트워크 가장자리의 AWS 서비스**: WaveLength Zones는 AWS 서비스를 5G 네트워크의 가장자리로 가져와, 통신 제공업체의 데이터센터에 직접 통합합니다.
• **서비스 예시**: EC2, EBS, VPC 등의 AWS 서비스가 포함됩니다.
• **극저지연 애플리케이션**: 5G 네트워크를 통해 매우 낮은 지연 시간을 자랑하는 애플리케이션을 지원합니다.
• **네트워크 내 트래픽 유지**: 트래픽은 통신 서비스 제공업체(CSP)의 네트워크를 벗어나지 않습니다.
• **고대역폭 및 보안 연결**: 부모 AWS 리전과의 고대역폭 및 보안 연결을 제공합니다.
• **추가 요금 또는 서비스 계약 없음**: 이러한 서비스를 사용하기 위한 추가 요금이나 서비스 계약이 필요하지 않습니다.

**사용 사례**
WaveLength Zones는 다음과 같은 다양한 사용 사례에 적합합니다:

• **스마트 시티**
• **기계학습을 이용한 진단**
• **연결된 차량**
• **인터랙티브 라이브 비디오 스트리밍**
• **증강 현실/가상 현실**
• **실시간 게임**

AWS Local Zones
AWS Local Zone은 AWS의 컴퓨팅, 스토리지, 데이터베이스 및 선택된 다른 AWS 서비스를 사용자에게 더 가깝게 배치하여 지연 시간에 민감한 애플리케이션을 운영할 수 있도록 설계되었습니다. 

### AWS Local Zone 특징
- **지연 시간 감소**: 사용자의 지연 시간에 민감한 애플리케이션을 위해 AWS 서비스를 최종 사용자에게 더 가까이 배치합니다.
- **VPC 확장**: AWS Local Zone을 통해 가상 사설 클라우드(VPC)를 더 많은 위치로 확장할 수 있으며, 이는 "AWS 리전의 확장"으로 간주됩니다.
- **호환 가능 서비스**: EC2, RDS, ECS, EBS, ElastiCache, Direct Connect 등과 호환됩니다.

### 예시
- **AWS 리전**: 북 버지니아(us-east-1)
- **AWS Local Zones**: 보스턴, 시카고, 댈러스, 휴스턴, 마이애미 등

AWS Local Zone을 사용하면 AWS 리전과 연결되어 있지만, 특정 도시나 지역에 위치하여 그 지역의 최종 사용자에게 낮은 지연성으로 서비스를 제공할 수 있습니다. 이러한 방식은 고속의 응답성이 요구되는 애플리케이션에 이상적이며, 사용자 경험을 향상시킬 수 있습니다.

