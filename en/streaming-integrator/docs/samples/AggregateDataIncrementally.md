
## Purpose:
This application demonstrates how to get running statistics using siddhi.
The sample depicts a scenario in a sweet production factory.

## Prerequisites:
1) Ensure that MySql is installed on your machine.
2) Add mysql jdbc driver into {WSO2_SI_HOME}/lib
2.1) Download JDBC driver (https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-5.1.45.tar.gz)
2.2) Extract it and use the "jartobundle" tool in {WSO2_SI_Home}/bin to convert the above jars into OSGi bundles.
## For Windows: <SI_HOME>/bin/jartobundle.bat <PATH_OF_DOWNLOADED_JAR> <PATH_OF_CONVERTED_JAR>
## For Linux: <SI_HOME>/bin/jartobundle.sh <PATH_OF_DOWNLOADED_JAR> <PATH_OF_CONVERTED_JAR
2.3) Copy over the converted bundles to the {WSO2_SI_Home}/lib directory .
3) Create a data store named 'sweetFactoryDB' in MySQL with relevant access privileges.
4) Replace the 'jdbc.url', username and password parameter values in the sample.
## eg : jdbc.url - jdbc:mysql://localhost:3306/sweetFactoryDB'
username - root
password - root
5) Save this sample        

## Executing the Sample:
1) Start the Siddhi application by clicking on 'Run'
2) If the Siddhi application starts successfully, the following messages would be shown on the console
* AggregateDataIncrementally.siddhi - Started Successfully!.

## Testing the Sample:
1) Click on 'Event Simulator' (double arrows on left tab)
2) Click 'Single Simulation' (this will be already selected)
3) Select 'AggregateDataIncrementally' as Siddhi App Name
4) Select 'RawMaterialStream' as StreamName
5) Provide attribute values
- name: chocolate cake
- amount: 100
6) Send event
7) Provide attribute values
- name: chocolate cake
- amount: 200
8) Send event
9) Provide attribute values
- name: chocolate ice cream
- amount: 50
10) Send event
11) Provide attribute values
- name: chocolate ice cream
- amount: 150
12) Send event
13) Select 'TriggerStream' as StreamName
14) Provide attribute values
- triggerId: 1
15) Send event

## Viewing the Results:
See the input and respective output on the console similar to the following (timestamp will be different).
INFO {io.siddhi.core.stream.output.sink.LogSink} - AggregateDataIncrementally : RawMaterialStatStream : [Event{timestamp=1513612116450, data=[1537862400000, chocolate ice cream, 100.0], isExpired=false}, Event{timestamp=1513612116450, data=[chocolate cake, 150.0], isExpired=false}]
[INFO {io.siddhi.core.stream.output.sink.LogSink} - AggregateDataIncrementally : RawMaterialStatStream : [Event{timestamp=1513612116450, data=[1537862400000, chocolate ice cream, 100.0], isExpired=false}, Event{timestamp=1513612116450, data=[chocolate cake, 150.0], isExpired=false}]


```sql
@App:name("AggregateDataIncrementally")

@App:description('Aggregates values every second until year and gets statistics')


define stream RawMaterialStream (name string, amount double);

@sink(type ='log')
define stream RawMaterialStatStream (AGG_TIMESTAMP long, name string, avgAmount double);

@store( type="rdbms",
        jdbc.url="jdbc:mysql://localhost:3306/sweetFactoryDB",
        username="root",
        password="root",
        jdbc.driver.name="com.mysql.jdbc.Driver")
define aggregation stockAggregation
from RawMaterialStream
select name, avg(amount) as avgAmount, sum(amount) as totalAmount
group by name
aggregate every sec...year;

define stream TriggerStream (triggerId string);

@info(name = 'query1')
from TriggerStream as f join stockAggregation as s
within "2016-06-06 12:00:00 +05:30", "2020-06-06 12:00:00 +05:30"
per 'hours'
select AGG_TIMESTAMP, s.name, avgAmount
insert into RawMaterialStatStream;
```