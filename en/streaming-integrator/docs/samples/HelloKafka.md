

## Purpose:
This application demonstrates how to use the Kafka transport in Siddhi to receive and publish events. Events which are in JSON format are consumed from one Kafka topic and written to another Kafka topic in XML format.

## Prerequisites:
1) The following steps must be executed to enable WSO2 SP to receive and publish events via the Kafka transport. Since you need to shut down the server to execute these steps, get a copy of these instructions prior to proceeding.
a) Download the Kafka broker from here: https://www.apache.org/dyn/closer.cgi?path=/kafka/0.10.2.1/kafka_2.11-0.10.2.1.tgz
b) Convert and copy the Kafka client jars from the {KafkaHome}/libs directory to the {WSO2SIHome}/libs directory as follows.
i) Create a directory named {Source} in a preferred location in your machine and copy the following JARs to it from the {KafkaHome}/libs directory.
* kafka_2.11-0.10.2.1.jar
* kafka-clients-0.10.2.1.jar
* metrics-core-2.2.0.jar
* scala-library-2.11.8.jar
* scala-parser-combinators_2.11-1.0.4.jar
* zkclient-0.10.jar
* zookeeper-3.4.9.jar
ii) Create another directory named {Destination} in a preferred location in your machine.
iii) To convert all the Kafka jars you copied into the {Source} directory, issue the following command,
* For Windows: {WSO2SIHome}/bin/jartobundle.bat <{Source} Directory Path> <{Destination} Directory Path>
* For Linux: sh {WSO2SIHome}/bin/jartobundle.sh <{Source} Directory Path> <{Destination} Directory Path>
iv) Add the OSGI converted kafka libs from {Destination} directory to {WSO2SIHome}/lib
v) Add the original Kafka libs from {Source} to {WSO2SIHome}/samples/sample-clients/lib
vi) Navigate to {KafkaHome} and start zookeeper node using "sh bin/zookeeper-server-start.sh config/zookeeper.properties" command
vii) Navigate to {KafkaHome} and start Kafka server node using "sh bin/kafka-server-start.sh config/server.properties" command
viii) Start the server: sh streaming-integrator-tooling.sh
2) Save this sample

## Executing the Sample:
1) Start the Siddhi application by clicking on 'Run'
2) If the Siddhi application starts successfully, the following messages would be shown on the console,
* HelloKafka.siddhi - Started Successfully!

## Testing the Sample:
1) Navigate to {WSO2SIHome}/samples/sample-clients/kafka-consumer and run the following command
ant -DtopicList=kafka_result_topic -Dtype=xml -DpartitionList=0
2) Navigate to {WSO2SIHome}/samples/sample-clients/kafka-producer and run the following command
ant -DtopicName=kafka_topic -DfilePath={WSO2SIHome}/samples/artifacts/HelloKafka/kafka_sample.txt
This command would publish the events in kafka_sample file to the Source Kafka Topic (named 'kafka_topic').

## Viewing the Results:
See the output events received by Sink Kafka Topic (named 'kafka_result_topic') being logged on the 'kafka-consumer' console. Note how the events have been converted from JSON to XML type. This conversion happens due to the Sink configuration's map type being XML.

*/
/*
SweetProductionStream definition. It receives events from "kafka_topic" in json format. Events in this stream will
have information about the name of the sweet and how much of it is produced.
*/
/*
Suppose that the factory packs sweets by taking last 3 sweet productions disregarding their individual amount.
TotalStream will have total of the last 3 events in SweetProductionStream. This is calcuklated as follows; the sum of
1st, 2nd, and 3rd events of SweetProductionStream will be the 1st event of TotalStream and the sum of 4th, 5th, and
6th events of SweetProductionStream will be the 2nd event of TotalStream
*/
/*
This stream counts the event number of TotalStream and sends that count along with total. This will help us find out
he batch which has a low total weight by using the count as batch number as we will see in the LowProductionAlertStream
*/
/*
This stream will send an alert into kafka_result_topic if any batch has a total weight less than 10. Batch number of
he low weight batch and the actual weight will be sent out.

```sql
@App:name("HelloKafka")

@App:description('Consume events from a Kafka Topic and publish to a different Kafka Topic')


@source(type='kafka',
        topic.list='kafka_topic',
        partition.no.list='0',
        threading.option='single.thread',
        group.id="group",
        bootstrap.servers='localhost:9092',
        @map(type='json'))
define stream SweetProductionStream (name string, amount double);

define stream TotalStream (total double);

define stream TotalStreamWithBatch(batchNumber long, total double);

@sink(type='kafka',
      topic='kafka_result_topic',
      bootstrap.servers='localhost:9092',
      partition.no='0',
      @map(type='xml'))
define stream LowProductionAlertStream (batchNumber long, lowTotal double);


--summing events in SweetProductionStream in batches of 3 and sending to TotalStream
@info(name='query1')
from SweetProductionStream#window.lengthBatch(3)
select sum(amount) as total
insert into TotalStream;

--count is included to indicate batch number
from TotalStream
select count() as batchNumber, total
insert into TotalStreamWithBatch;

--filtering out events with total less than 10 to create alert
from TotalStreamWithBatch[total < 10]
select batchNumber, total as lowTotal
insert into LowProductionAlertStream;
```