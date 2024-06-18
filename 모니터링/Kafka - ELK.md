
TroubleShooting

version error -> 8.10.2 -> 8.14.1
과거 버전 사용시: Could not load codec 'Lucene99'. Did you forget to add lucene-backward-codecs. jar?

문제: "message":"Authentication of [kibana_system] was terminated by realm [reserved] - failed to authenticate user [kibana_system]",
https://github.com/deviantony/docker-elk/issues/863
https://github.com/deviantony/docker-elk/issues/729

1. elasticsearch.yml
```
cluster.routing.allocation.disk.threshold_enabled: true
cluster.routing.allocation.disk.watermark.flood_stage: 2gb
cluster.routing.allocation.disk.watermark.low: 4gb
cluster.routing.allocation.disk.watermark.high: 3gb
```

2. Password policy
```
# https://www.elastic.co/guide/en/elasticsearch/reference/current/built-in-users.html

ELASTIC_PASSWORD='dlengus'

# User 'logstash_internal' (custom)

#

# The user Logstash uses to connect and send data to Elasticsearch.

# https://www.elastic.co/guide/en/logstash/current/ls-security.html

LOGSTASH_INTERNAL_PASSWORD='changeme'

# User 'kibana_system' (built-in)

#

# The user Kibana uses to connect and communicate with Elasticsearch.

# https://www.elastic.co/guide/en/elasticsearch/reference/current/built-in-users.html

KIBANA_SYSTEM_PASSWORD='changeme'
```
https://ldh-6019.tistory.com/455


3. logstash.conf

```
input {

kafka {

bootstrap_servers => "kafka:29092"

topics => ["ek-log"]

consumer_threads => 1 #각 토픽에 대해 생성할 컨슈머 스레드의 수 (기본 값 : 1) 1 = 순서 보장

decorate_events => true

}

}

## Add your filters / logstash plugins configuration here

output {

elasticsearch {

hosts => "elasticsearch:9200"

user => "logstash_internal"

password => "${LOGSTASH_INTERNAL_PASSWORD}"

index => "ek-log-2024-06-18"

}

}
```

4. settings
	```
	action [indices:admin/auto_create] is unauthorized for user [logstash_internal]
	```

	1. logstash_internal user -> roles , create_index, manage, all, auto_configure
	2. indices - ek-log-*
	3. https://github.com/deviantony/docker-elk/issues/687
	4. https://discuss.elastic.co/t/action-indices-admin-create-is-unauthorized-for-user/303458
	5. https://stackoverflow.com/questions/74229113/logstash-cant-index-into-es-action-indicesadmin-auto-create-is-unauthorize

	무슨 역할일까?

![[Pasted image 20240618210907.png]]

5. UI에는 kibana-system으로 접근할 수없다
https://discuss.elastic.co/t/you-do-not-have-permission-to-access-requested-page-kibana-help/274253