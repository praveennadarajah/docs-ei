
## Purpose:
This application demonstrates how to configure WSO2 Streaming Integrator Tooling to receive events to the SweetProductionStream via rabbitmq broker in JSON format using default mapping and log the events in LowProductionAlertStream to the output console.

## Prerequisites:
1) Save this sample
2) Install the rabbitmq server using the following command
sudo apt-get install rabbitmq-server
3) To enable RabbitMQ Management Console, run the following:
sudo rabbitmq-plugins enable rabbitmq_management
4) To start the service, issue the following command:
invoke-rc.d rabbitmq-server start

## Executing the Sample:
1) Start the Siddhi application by clicking on 'Run'.
2) If the Siddhi application starts successfully, the following messages would be shown on the console.
* ReceiveRabbitmqInJSONFormat.siddhi - Started Successfully!
Check whether the exchange 'rabbitmq_sample' is created in the rabbitmq server or not. To check that you can visit http://localhost:15672/

## Testing the Sample:
## Publish events with rabbitmq sample publisher:
Open a terminal and issue command from the {WSO2SIHome}/samples/sample-clients/rabbitmq-producer and run 'ant' command.
If you want to publish custom number of events, you need to run 'ant' command as follows:
ant -DnoOfEventsToPublish=5

## Viewing the Results:
See the output. Following message would be shown on the console.
INFO {io.siddhi.core.stream.output.sink.LogSink} - ReceiveRabbitmqInJSONFormat : LowProducitonAlertStream : Event{timestamp=1513233900122, data=[Lollipop, 6186.0], isExpired=false}
INFO {io.siddhi.core.stream.output.sink.LogSink} - ReceiveRabbitmqInJSONFormat : LowProducitonAlertStream : Event{timestamp=1513233901122, data=[Donut, 7904.0], isExpired=false}
INFO {io.siddhi.core.stream.output.sink.LogSink} - ReceiveRabbitmqInJSONFormat : LowProducitonAlertStream : Event{timestamp=1513233902124, data=[Honeycomb, 4495.0], isExpired=false}
INFO {io.siddhi.core.stream.output.sink.LogSink} - ReceiveRabbitmqInJSONFormat : LowProducitonAlertStream : Event{timestamp=1513233903125, data=[Donut, 1393.0], isExpired=false}


```sql
@App:name("ReceiveRabbitmqInJSONFormat")
@app:description("Receives the events from the rabbitmq broker using the AMQP protocol.")

@source(type='rabbitmq', uri = 'amqp://guest:guest@localhost:5672', exchange.name = 'rabbitmq_sample',  @map(type='json'))
define stream SweetProductionStream (name string, amount double);

@sink(type='log')
define stream LowProductionAlertStream (name string, amount double);

-- passthrough data in the SweetProductionStream into LowProductionAlertStream
@info(name='query1')
from SweetProductionStream
select *
insert into LowProductionAlertStream;
```