## SQS - Decouple
![[스크린샷 2024-06-14 오후 4.55.49.png]]

1. **확장성**: SQS는 초당 1개 메시지부터 수천 개의 메시지까지 처리할 수 있어, 작은 규모부터 대규모까지의 요구 사항을 충족시킬 수 있습니다.
2. **메시지 보관 기간**: 기본적으로 메시지는 4일 동안 보관되며, 최대 14일까지 설정 가능합니다.
3. **메시지 한도 없음**: 큐에 저장할 수 있는 메시지의 수에 제한이 없습니다.
4. **메시지 삭제**: 소비자가 메시지를 읽은 후에는 자동으로 삭제됩니다.
5. **저지연성**: 메시지 발행과 수신 시 지연 시간이 10밀리초 미만입니다.
6. **소비자의 작업 분담**: 여러 소비자가 메시지를 읽는 작업을 공유하며, 수평적으로 확장할 수 있습니다.

FIFO Queue = First in First Out (ordering of messages in the queue)

## Amazon Kinesis = real-time big data streaming
- Managed service to collect, process, and analyze real-time streaming data at any scale
![[스크린샷 2024-06-15 오후 5.05.40.png]]

## Amazon SNS
- event publishers - send message to one SNS topic
- as Many Event Subscribers
- Up to 12,500,000 subscriptions per topic, 100,000 topics limit
![[스크린샷 2024-06-15 오후 5.07.02.png]]

## Amazon MQ
- - When migrating to the cloud, instead of re-engineering the application to use SQS and SNS, we can use Amazon MQ
- no scale, multi-AZ, has both queue feature and topic feataures

