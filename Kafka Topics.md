Topics: a Particular Stream of Data
- Similar to Table in database
- as many topics as you want 
- identified by name
- any kind of message format
- sequence of message is calle data stream
- kafka => data streaming platform
- cannot query topics, but use consumers to read the data

Partitions and Offsets
- Topics are split in partition
- Messages within each partition are ordered
- Each message with in a partition gets an incremental id, called offset

Kafka topics are immutable: once data is written to a partition, it cannot be changed

![[스크린샷 2024-06-06 오후 5.13.16.png]]

truck_gps 
truck => gps position to Kafka (truck id, truck position) => topic truck_gps that contains the position of all trucks
we choose to create that topic with 10 paritition

![[스크린샷 2024-06-06 오후 5.15.28.png]]

- Once the data is written to a partition, it cannot be changed (immutability)
- ﻿﻿Data is kept only for a limited time (default is one week - configurable)
- ﻿﻿Offset only have a meaning for a specific partition.
	- ﻿﻿E.g. offset 3 in partition 0 doesn't represent the same data as offset 3 in partition 1
	- ﻿﻿Offsets are not re-used even if previous messages have been deleted
- ﻿﻿Order is guaranteed only within a partition (not across partitions)
- ﻿﻿Data is assigned randomly to a partition unless a key is provided (more on this later)

