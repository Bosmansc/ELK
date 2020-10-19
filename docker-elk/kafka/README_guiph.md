# Docker kafka plus zookeeper image

### Build image

> docker build ./docker-kafka -t <your_docker_image_name>

### RUN

> docker run docker-kafka-test <your_docker_image_name>

### zookeeper shell (helps determine the kafka brocker port)

- connect to zookeeper shell

> /opt/kafka_2.12-2.3.0/bin/zookeeper-shell.sh localhost:2181

- list the topics

> ls /brokers/topics

- list the brockers ids

> ls /borkers/id

- collect information about a specific broker (id 0) ==> see endpoints

> get /brokers/ids/0

```
{"listener_security_protocol_map":{"PLAINTEXT":"PLAINTEXT","INTERNAL":"PLAINTEXT"},"endpoints":["PLAINTEXT://kafka:443","INTERNAL://localhost:9093"],"jmx_port":-1,"host":"kafka","timestamp":"1567506643562","port":443,"version":4}
cZxid = 0x18
ctime = Tue Sep 03 10:30:43 UTC 2019
mZxid = 0x18
mtime = Tue Sep 03 10:30:43 UTC 2019
pZxid = 0x18
cversion = 0
dataVersion = 1
aclVersion = 0
ephemeralOwner = 0x1001212838a0000
dataLength = 229
numChildren = 0
```

### kafka create topic

 > /opt/kafka_2.12-2.3.0/bin/kafka-topics.sh --create --topic testme --replication-factor 1 --partitions 4 --zookeeper localhost:2181

### kafka list topics

> /opt/kafka_2.12-2.3.0/bin/kafka-topics.sh --list --zookeeper localhost:2181

### kafka producer example

> /opt/kafka_2.12-2.3.0/bin/kafka-console-producer.sh --broker-list localhost:9092 --topic testme

### kafka consumer exemple

> /opt/kafka_2.12-2.3.0/bin/kafka-console-consumer.sh --bootstrap-server localhost:9093 --from-beginning --group group1 --topic testme


# launch metricbeat

### enable kafka module

> ./metricbeat modules enable kafka

### change metricbeat.yml config file

> uncomment output url, user and password (by default easticsearch:9200,  elastic, changeme)
