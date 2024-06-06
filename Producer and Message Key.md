Producers write data to topics(which are made of parititions)
Producer know to which parition to write to 
In case broker failures, Producer will automatically recover
![[스크린샷 2024-06-06 오후 5.18.03.png]]

Message Keys
- Producer can choose to send a key with the message
- if key =null, data is sent round robin
- if key != null, that all messages for that key will always go to the same parition (hashing)
- key are typically sent if you need ordering for a specific field(truck_id)
![[스크린샷 2024-06-06 오후 5.20.00.png]]

![[스크린샷 2024-06-06 오후 5.20.46.png]]

Kafka Message Serializer
- Kafka only accepts bytes as an input from producers and sends bytes out as an output to consumers
- Message Serialization means transforming objects/ data into bytes
- They are used on the value and the key
- Common Serializers
	- String
	- Int, FLot
	- Avro
	- Protobuf

![[스크린샷 2024-06-06 오후 5.21.58.png]]

![[스크린샷 2024-06-06 오후 5.24.03.png]]

