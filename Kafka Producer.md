```
// create Producer Properties  
Properties properties = new Properties();  

// connect to Localhost  
//        properties.setProperty("bootstrap.servers", "127.0.0.1:9092");  

// set producer properties  
properties.setProperty("key.serializer", StringSerializer.class.getName());  
properties.setProperty("value.serializer", StringSerializer.class.getName());  

// create the Producer  
KafkaProducer<String, String> producer = new KafkaProducer<>(properties);  

// create a Producer Record  
ProducerRecord<String, String> producerRecord =  
		new ProducerRecord<>("demo_java", "hello world");  

// send data  
producer.send(producerRecord);  

// tell the producer to send all data and block until done -- synchronous  
producer.flush();  

// flush and close the producer  
producer.close();
```

**Kafka 프로듀서 설정**
1. **KafkaProducer 생성**:
• KafkaProducer<String, String> 타입을 사용하여, 프로듀서 인스턴스를 생성합니다. 여기서 String, String은 키와 값의 데이터 타입을 의미하며, 두 데이터 타입 모두 문자열임을 나타냅니다.
• new KafkaProducer<>(properties)를 통해 새로운 프로듀서 객체를 생성하며, 설정한 프로퍼티를 생성자에 전달합니다.

2. **프로듀서 프로퍼티 설정**:
• 프로듀서에 필요한 프로퍼티들(예: key.serializer, value.serializer)을 설정하여 Kafka와의 연결 방식 및 키와 값의 직렬화 방법을 지정합니다.

**메시지 전송**
1. **ProducerRecord 생성**:
• ProducerRecord<String, String> 타입을 사용하여, Kafka로 보낼 레코드를 생성합니다. 이 예제에서는 demo_java라는 토픽과 hello world라는 값을 사용합니다.
• new ProducerRecord<>("demo_java", "hello world") 생성자를 호출하여, 특정 토픽에 메시지를 전송할 레코드를 생성합니다.

2. **데이터 전송 및 처리**:
• producer.send(producerRecord)를 호출하여 메시지를 비동기적으로 전송합니다.
• producer.flush()를 호출하여 모든 메시지가 전송되기를 기다립니다. 이는 데이터 전송이 완료될 때까지 프로듀서가 차단되는 동기식 작업입니다.
• producer.close()를 호출하여 프로듀서 인스턴스를 닫고, 모든 자원을 정리합니다. 이 메소드는 내부적으로 flush()를 호출하기 때문에, 명시적으로 flush()를 호출할 필요는 없지만, 필요한 경우 사용할 수 있습니다.

**Kafka 토픽 설정**
• Kafka 토픽 demo_java는 세 개의 파티션을 갖도록 설정하며, 이는 메시지의 분산 처리에 도움이 됩니다. 이 토픽은 CLI 또는 관리 콘솔을 통해 생성할 수 있습니다.

**결과 확인**
• Kafka 토픽에서 hello world 메시지를 확인할 수 있으멈, 이는 토픽의 데이터가 정상적으로 전송되었음을 나타냅니다. 메시지 확인은 Kafka 콘솔 컨슈머를 사용하거나 토픽의 데이터를 직접 조회하여 확인할 수 있습니다.

## Callbacks
- StickyPartitioner
```
ProducerRecord<String, String> producerRecord =  
        new ProducerRecord<>("demo_java", "hello world " + i);  
  
// send data  
producer.send(producerRecord, new Callback() {  
    @Override  
    public void onCompletion(RecordMetadata metadata, Exception e) {  
        // executes every time a record successfully sent or an exception is thrown  
        if (e == null) {  
            // the record was successfully sent  
            log.info("Received new metadata \n" +  
                    "Topic: " + metadata.topic() + "\n" +  
                    "Partition: " + metadata.partition() + "\n" +  
                    "Offset: " + metadata.offset() + "\n" +  
                    "Timestamp: " + metadata.timestamp());  
        } else {  
            log.error("Error while producing", e);  
        }  
    }  
});
```


```
properties.setProperty("partitioner.class",RoundRobinPartitioner.class.getName())
```

**Kafka 프로듀서의 파티셔닝 전략**
1. **스티키 파티셔너 (Sticky Partitioner)**

• **개요**: 스티키 파티셔너는 메시지를 파티션에 더 효율적으로 배분하기 위해 배치 처리 방식을 사용합니다.

• **작동 방식**: 연속적인 메시지들을 배치로 묶어 한 번에 같은 파티션으로 전송합니다. 이를 통해 네트워크 호출 수를 줄이고 전송 효율을 높입니다.

• **예시**: 첫 번째 세 메시지가 빠르게 도착하면 이를 하나의 배치로 묶어 파티션 1로 전송, 이어서 다음 세 메시지를 다른 파티션으로 전송합니다.

2. **유니폼 파티셔너 (Uniform Partitioner)**

• **개요**: 키가 지정되지 않은 메시지에 대해 사용되며, Kafka 로그에서 partitioner.class 값이 ‘null’인 것으로 확인됩니다.

• **설정**: 이 파티셔너는 라운드 로빈 방식 대신, 스티키 파티셔너를 포함해 다양한 방식으로 동작할 수 있습니다.

**테스트 및 설정**
• **프로듀서 설정**: 프로듀서는 로그를 통해 자세한 실행 정보를 제공하며, 이는 개발자가 프로듀서의 동작을 이해하는 데 도움을 줍니다.

• **메시지 전송 테스트**: 예를 들어 30개의 레코드를 배치로 생성하고, 각 배치 전송 후 500ms의 지연을 주어 프로듀서의 배치 처리 성능을 테스트할 수 있습니다.

• **배치 크기 조절**: batch.size 프로퍼티를 작은 값(예: 400)으로 설정해 다양한 파티션으로 메시지가 어떻게 배분되는지 관찰합니다.

  
**결과 확인 및 파티션 관찰**
• **로그 확인**: 프로듀서 로그를 통해 어떤 파티션으로 메시지가 전송되었는지 확인할 수 있습니다. 로그를 통해 파티션 0, 1, 2에 메시지가 골고루 전송된 것을 볼 수 있습니다.

• **실제 사용 주의**: 이러한 테스트 방식은 개발 또는 테스트 환경에서는 유용하지만, 실제 운영 환경에서는 특정 파티션에만 메시지를 전송하는 방식을 피해야 합니다


## Producer and Key
```
String topic = "demo_java";  
String key = "id_" + i;  
String value = "hello world " + i;  
  
// create a Producer Record  
ProducerRecord<String, String> producerRecord =  
        new ProducerRecord<>(topic, key, value);  
  
// send data  
producer.send(producerRecord, new Callback() {  
    @Override  
    public void onCompletion(RecordMetadata metadata, Exception e) {  
        // executes every time a record successfully sent or an exception is thrown  
        if (e == null) {  
            // the record was successfully sent  
            log.info("Key: " + key + " | Partition: " + metadata.partition());  
        } else {  
            log.error("Error while producing", e);  
        }  
    }  
});
```

아이디를 정해서 보내게 된다면

id_1, id_3, id_6는 0번 파티션으로 갔습니다

id_2, id_4, id_5, 7, 9는 2번 파티션으로 갔고요

id_0, 8은 1번 파티션으로 갔어요

그리고 두 번째 배치에서

다시 id_2, id_4, id_5를 보냈을 때

보시는 것처럼 id_2, id_4, id_5는

앞서 id_2, id_4, id_5와 마찬가지로

2번 파티션으로 전송됐습니다, 마찬가지로
