

## Purpose:
This application demonstrates how to receive events via TCP transport and carryout data pre-processing with numerous Siddhi extensions (eg. string extension, time extension). For more information on Siddhi extensions please refer to "https://wso2.github.io/siddhi/extensions/". In this sample, a composite ID is obtained using string concatenation and the time format of the incoming event is altered from 'yyyy/MM/dd HH:mm:ss' to 'dd-MM-yyyy HH:mm:ss'

## Prerequisites:
1) Save this sample

## Executing the Sample:
1) Start the Siddhi application by clicking on 'Run'
2) If the Siddhi application starts successfully, the following messages would be shown on the console
* Tcp Server started in 0.0.0.0:9892
* DataPreprocessing.siddhi - Started Successfully!
## Notes:
If you edit this application while it's running, stop the application -> Save -> Start.

## Testing the Sample:
1) Navigate to {WSO2SIHome}/samples/sample-clients/tcp-client and run the "ant" command as follows.
ant -Dtype=json -DfilePath={WSO2SIHome}/samples/artifacts/DataPreprocessing/data_preprocessing_events.txt
-DeventDefinition='{"event":{"id":"{0}","value":{1},"property":{2},"plugId":{3},"householdId":{4},"houseId":{5},"currentTime":"{6}"}}' -Durl=tcp://localhost:9892/SmartHomeStream

## Viewing the Results:
See the output. Following messages would be shown on the console.
INFO {io.siddhi.core.stream.output.sink.LogSink} - DataPreprocessing : ProcessedStream : Event{timestamp=1513621173211, data=[HouseholdID:1::UniqueID:0001, 12.12, 13-08-2001 23:49:33], isExpired=false}
INFO {io.siddhi.core.stream.output.sink.LogSink} - DataPreprocessing : ProcessedStream : Event{timestamp=1513621174202, data=[HouseholdID:2::UniqueID:0002, 13.12, 13-08-2004 23:49:34], isExpired=false}
INFO {io.siddhi.core.stream.output.sink.LogSink} - DataPreprocessing : ProcessedStream : Event{timestamp=1513621175208, data=[HouseholdID:3::UniqueID:0003, 13.12, 13-08-2006 23:49:35], isExpired=false}
INFO {io.siddhi.core.stream.output.sink.LogSink} - DataPreprocessing : ProcessedStream : Event{timestamp=1513621176214, data=[HouseholdID:4::UniqueID:0004, 14.12, 13-08-2008 23:49:36], isExpired=false}



```sql
@App:name("DataPreprocessing")

@App:description('Collect data via TCP transport and pre-process')


@Source(type = 'tcp',
        context='SmartHomeStream',
        @map(type='json'))
define stream SmartHomeStream (id string, value float, property bool, plugId int, householdId int, houseId int, currentTime string);

@sink(type='log')
define stream ProcessedStream (compositeID string, value float, formattedTime string);

--Process smart home data by concatenating the IDs and formatting the time
@info(name='query1')
from SmartHomeStream
select str:concat("HouseholdID:", convert(householdId, "string"), "::", "UniqueID:", id) as compositeID, value, str:concat(currentTime, " ", time:currentTime()) as formattedTime
insert into ProcessedStream;
```