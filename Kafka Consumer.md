```
// create consumer configs  
properties.setProperty("key.deserializer", StringDeserializer.class.getName());  
properties.setProperty("value.deserializer", StringDeserializer.class.getName());  
properties.setProperty("group.id", groupId);  
properties.setProperty("auto.offset.reset", "earliest");
```

. **직렬화 및 역직렬화 설정**:
• 프로듀서는 데이터를 Kafka로 전송하기 전에 바이트로 직렬화합니다.
• 컨슈머는 Kafka로부터 데이터를 받아 실제 객체로 역직렬화합니다.
• key.deserializer와 value.deserializer 프로퍼티에 StringDeserializer 클래스를 설정하여 문자열 데이터를 처리합니다.

2. **그룹 ID 설정**:
• 컨슈머는 특정 그룹 ID (group.id)를 사용하여 구성됩니다.
• 예제에서는 my-java-application이라는 그룹 ID를 사용합니다.

3. **오프셋 리셋 설정**:
• auto.offset.reset 프로퍼티를 설정하여 컨슈머가 토픽의 메시지를 어느 시점부터 읽을지 결정합니다.
• earliest 옵션은 토픽의 시작부터, latest는 가장 최신부터 메시지를 읽기 시작합니다.

Deserializer setting을 해야한다

```
// create a consumer  
KafkaConsumer<String, String> consumer = new KafkaConsumer<>(properties);  
  
// subscribe to a topic  
consumer.subscribe(Arrays.asList(topic));
```

**Kafka 컨슈머 작업**
1. **토픽 구독**:

• 컨슈머는 demo_java 토픽을 구독합니다.

• subscribe() 메소드를 사용하여 토픽을 리스트로 넘겨 구독합니다.

Consumer를 만들고 토픽에 구독을 한 다음

```
while (true) {  
  
    log.info("Polling");  
  
    ConsumerRecords<String, String> records =  
            consumer.poll(Duration.ofMillis(1000));  
  
    for (ConsumerRecord<String, String> record: records) {  
        log.info("Key: " + record.key() + ", Value: " + record.value());  
        log.info("Partition: " + record.partition() + ", Offset: " + record.offset());  
    }  
  
  
}
```

 **데이터 폴링 및 처리**:

• while(true) 루프를 사용하여 Kafka로부터 데이터를 지속적으로 폴링합니다.

• poll() 메소드를 호출하여 Kafka로부터 데이터를 받아오며, 이 메소드는 지정된 시간 동안 데이터를 기다립니다.

• 폴링된 데이터는 ConsumerRecord<String, String> 타입의 컬렉션으로 반환됩니다.

## Gracefully Shutdown
- Exception 터지면 shutdown

## Advanced
https://www.conduktor.io/kafka/advanced-kafka-consumer-with-java/