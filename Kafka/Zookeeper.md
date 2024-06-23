- ﻿﻿Zookeeper manages brokers (keeps a list of them)
- ﻿﻿Zookeeper helps in performing leader election for partitions
- ﻿﻿Zookeeper sends notifications to Kafka in case of changes (e.g. new topic, broker dies, broker comes up, delete topics, etc....)
- ﻿﻿Kafka 2.x can't work without Zookeeper
- ﻿﻿Kafka 3.x can work without Zookeeper (KIP-500) - using Kafka Raft instead
- ﻿﻿Kafka 4.x will not have Zookeeper
- ﻿﻿Zookeeper by design operates with an odd number of servers (1, 3, 5, 7)
- ﻿﻿Zookeeper has a leader (writes) the rest of the servers are followers (reads)
- ﻿﻿(Zookeeper does NOT store consumer offsets with Kafka > v0.10)

![[스크린샷 2024-06-06 오후 5.46.08.png]]
With Kafka Brokers?
• Yes, until Kafka 4.0 is out while waiting for Kafka without Zookeeper to be production-ready


• With Kafka Clients?
• Over time the Kafka clients and CLI have been migrated to leverage the brokers as a connection endpoint instead of Zookeeper
• Sook eper as its epreets store offset in Kafka and Zookeeper and must not connect to
• Since Kafka 2.2, the kafka-topics.sh CLI command references Kafka brokers and not Zookeeper for topic management (creation, deletion, etc...) and the Zookeeper CLI argument is deprecated.
• All the APIs and commands that were previously leveraging Zookeeper are migrated to use Katka instead, so that when clusters are migrated to be without Zookeeper, the change is invisible to clients.
• Zookeeper is also less secure than Kafka, and therefore Zookeeper ports should only be opened to allow traffic from Kafka brokers, and not Kafka clients
