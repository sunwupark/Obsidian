All the consumers in an application read data as consumer groups
each consumer with in a group reads from exclusive patitions

![[스크린샷 2024-06-06 오후 5.27.54.png]]

![[Pasted image 20240606172829.png]]
- consumer4는 inactive

![[Pasted image 20240606172913.png]]
- 한 커슈머 그룹은 서비스 , 다른 그룹은 알림 서비스 등 같은 데이터를 여러개의 컨슈머에서 읽어야할때

## Consumer Offsets
- Kafka stores the offsets at which a consumer groups has been reading
- offsets committed are in kafka topic name __consumer_offsets
- When a consumer in a group has processed data received from Kafka, it should be periodically committing the offsets (the Kafka broker will write to __consumer_offsets, not the group itself)
- If a consumer dies, it will be able to read back from where it left off thanks to the committed consumer offsets!

## Delivery Semantics for consumers
- ﻿﻿By default, Java Consumers will automatically commit offsets (at least once)
- ﻿﻿There are 3 delivery semantics if you choose to commit manually
- ﻿﻿At least once (usually preferred)
	- ﻿﻿Offsets are committed after the message is processed
	- ﻿﻿If the processing goes wrong, the message will be read again
	- ﻿﻿This can result in duplicate processing of messages. Make sure your processing is idempotent (i.e. processing again the messages won't impact your systems)
- ﻿﻿At most once
	- ﻿﻿Offsets are committed as soon as messages are received
	- ﻿﻿If the processing goes wrong, some messages will be lost (they won't be read again)
- ﻿﻿Exactly once
	- ﻿﻿For Kafka => Kafka workflows: use the Transactional API (easy with Kafka Streams API)
	- ﻿﻿For Kafka => External System workflows: use an idempotent consumer

