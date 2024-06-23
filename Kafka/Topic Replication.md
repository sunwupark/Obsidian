- ﻿﻿Topics should have a replication factor > 1 (usually between 2 and 3)
- ﻿﻿This way if a broker is down, another broker can serve the data
- ﻿﻿Example: Topic-A with 2 partitions and replication factor of![[스크린샷 2024-06-06 오후 5.38.05.png]]

![[스크린샷 2024-06-06 오후 5.38.19.png]]

## Concept of Leader for a Partition
- At any time only ONE broker can be a leader for a given partition
- ﻿﻿Producers can only send data to the broker that is leader of a partition
- the other broker will replicate the data
- each partitoin has one leader and multiple isr(In-sync replica)
![[스크린샷 2024-06-06 오후 5.38.50.png]]

Default producer & consumer behavior with leaders

- ﻿﻿Kafka Producers can only write to the leader broker for a partition
- ﻿﻿Kafka Consumers by default will read from the leader broker for a partition
![[스크린샷 2024-06-06 오후 5.40.00.png]]

Kafka Consumer Replica Fetching
- Consumers to read from the closest replica

![[스크린샷 2024-06-06 오후 5.40.57.png]]
-> help improve latency, and also decrease network costs if using the cloud

