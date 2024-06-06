
About KRAFT
- ﻿﻿In 2020, the Apache Kafka project started to work to remove the Zookeeper dependency from it (KIP-500)
- ﻿﻿Zookeeper shows scaling issues when Kafka clusters have > 100,000 partitions
- ﻿﻿By removing Zookeeper, Apache Kafka can
- ﻿﻿Scale to millions of partitions, and becomes easier to maintain and set-up
- ﻿﻿Improve stability, makes it easier to monitor, support and administer
- ﻿﻿Single security model for the whole system
- ﻿﻿Single process to start with Kafka
- ﻿﻿Faster controller shutdown and recovery time
- ﻿﻿Kafka 3.X now implements the Raft protocol (KRaft) in order to replace Zookeeper
- ﻿﻿Production ready since Kafka 3.3.1 (KIP-833)
- Kafkak 4.0 will be released only with KRaft

![[스크린샷 2024-06-06 오후 5.49.46.png]]

![[스크린샷 2024-06-06 오후 5.49.57.png]]

