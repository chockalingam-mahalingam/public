# public
Install Apache Kafka on CentOS 8

yum update
yum install wget vim 
yum  install java-11-openjdk


wget https://downloads.apache.org/kafka/3.4.0/kafka_2.13-3.4.0.tgz 

tar xvf kafka_2.13-3.4.0.tgz 
sudo mv kafka_2.13-3.4.0 /usr/local/kafka 

vim /etc/systemd/system/zookeeper.service

[Unit]
Description=Apache Zookeeper server
Documentation=http://zookeeper.apache.org
Requires=network.target remote-fs.target
After=network.target remote-fs.target

[Service]
Type=simple
ExecStart=/usr/bin/bash /usr/local/kafka/bin/zookeeper-server-start.sh /usr/local/kafka/config/zookeeper.properties
ExecStop=/usr/bin/bash /usr/local/kafka/bin/zookeeper-server-stop.sh
Restart=on-abnormal

[Install]
WantedBy=multi-user.target


vim /etc/systemd/system/kafka.service

[Unit]
Description=Apache Kafka Server
Documentation=http://kafka.apache.org/documentation.html
Requires=zookeeper.service

[Service]
Type=simple
Environment="JAVA_HOME=/usr/lib/jvm/jre-11-openjdk"
ExecStart=/usr/bin/bash /usr/local/kafka/bin/kafka-server-start.sh /usr/local/kafka/config/server.properties
ExecStop=/usr/bin/bash /usr/local/kafka/bin/kafka-server-stop.sh

[Install]
WantedBy=multi-user.target



systemctl daemon-reload
sudo systemctl start zookeeper

sudo systemctl start kafka
sudo systemctl status kafka

#Creating Topics in Apache Kafka
cd /usr/local/kafka
bin/kafka-topics.sh --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic testTopic

# list topics
bin/kafka-topics.sh --list --zookeeper localhost:9092

#Apache Kafka Producer and Consumer

bin/kafka-console-producer.sh --broker-list localhost:9092 --topic testTopic

bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic testTopic --from-beginning
