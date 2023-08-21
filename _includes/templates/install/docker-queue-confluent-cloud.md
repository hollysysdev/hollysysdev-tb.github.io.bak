{% include templates/install/queue-confluent-cloud-config.md %}

Create docker compose file for ThingsBoard queue service:

```text
nano docker-compose.yml
```
{: .copy-code}

Add the following line to the yml file. Don’t forget to replace “CLUSTER_API_KEY”, "CLUSTER_API_SECRET" and "localhost:9092" with your real Confluent Cloud bootstrap servers:

```yml
version: '3.0'
services:
  mytb:
    restart: always
    image: "thingsboard/tb-postgres"
    ports:
      - "8080:9090"
      - "1883:1883"
      - "7070:7070"
      - "5683-5688:5683-5688/udp"
    environment:
      TB_QUEUE_TYPE: kafka
      TB_KAFKA_SERVERS: localhost:9092
      TB_QUEUE_KAFKA_REPLICATION_FACTOR: 3
      TB_QUEUE_KAFKA_USE_CONFLUENT_CLOUD: true
      TB_QUEUE_KAFKA_CONFLUENT_SASL_JAAS_CONFIG: org.apache.kafka.common.security.plain.PlainLoginModule required username="CLUSTER_API_KEY" password="CLUSTER_API_SECRET";}

      # These params affect the number of requests per second from each partitions per each queue.
      # Number of requests to particular Message Queue is calculated based on the formula:
      # ((Number of Rule Engine and Core Queues) * (Number of partitions per Queue) + (Number of transport queues)
      #  + (Number of microservices) + (Number of JS executors)) * 1000 / POLL_INTERVAL_MS
      # For example, number of requests based on default parameters is:
      # Rule Engine queues:
      # Main 10 partitions + HighPriority 10 partitions + SequentialByOriginator 10 partitions = 30
      # Core queue 10 partitions
      # Transport request Queue + response Queue = 2
      # Rule Engine Transport notifications Queue + Core Transport notifications Queue = 2
      # Total = 44
      # Number of requests per second = 44 * 1000 / 25 = 1760 requests
      # 
      # Based on the use case, you can compromise latency and decrease number of partitions/requests to the queue, if the message load is low.
      # By UI set the parameters - interval (1000) and partitions (1) for Rule Engine queues.
      # Sample parameters to fit into 10 requests per second on a "monolith" deployment: 
      TB_QUEUE_CORE_POLL_INTERVAL_MS: 1000
      TB_QUEUE_CORE_PARTITIONS: 2
      TB_QUEUE_RULE_ENGINE_POLL_INTERVAL_MS: 1000
      TB_QUEUE_TRANSPORT_REQUEST_POLL_INTERVAL_MS: 1000
      TB_QUEUE_TRANSPORT_RESPONSE_POLL_INTERVAL_MS: 1000
      TB_QUEUE_TRANSPORT_NOTIFICATIONS_POLL_INTERVAL_MS: 1000
      TB_QUEUE_VC_INTERVAL_MS: 1000
      TB_QUEUE_VC_PARTITIONS: 1
    volumes:
      - ~/.mytb-data:/data
      - ~/.mytb-logs:/var/log/thingsboard
```
{: .copy-code}

You can update default Rule Engine queues configuration using UI. More about ThingsBoard Rule Engine queues see in [documentation](/docs/{{docsPrefix}}user-guide/rule-engine-2-5/queues/).