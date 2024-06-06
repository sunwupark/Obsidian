Consumers read data from a topic - pull model
Consumeres automatically know which broker to read from
in case of borker failures, consumers know how to recover
Data is read in order from low to high offset within each partitions
![[스크린샷 2024-06-06 오후 5.25.39.png]]

#### Consumer Deserializer
- Deserialize indicates how to transform bytes into objects / data
- Theyare used on the value and the key of the message
![[스크린샷 2024-06-06 오후 5.26.26.png]]

- producer가 전송하는 타입을 변경하면 안된다

