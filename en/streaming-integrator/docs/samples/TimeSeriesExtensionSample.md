
## Purpose:
This application demonstrates how to use the siddhi-execution-timeseries with regress function which allows user to specify the batch size that defines the number of events to be considered for the calculation of regression.

## Prerequisites:
1) Start editor.
2) Save this sample.

## Executing the Sample:
1) Start the Siddhi application by clicking on 'Run'
2) If the Siddhi application starts successfully, the following messages would be shown on the console,
* TimeSeriesExtensionSample.siddhi - Started Successfully!

## Testing the Sample:
Navigate to {WSO2SIHome}/samples/sample-clients/http-client and run the following command
ant -Dtype=xml -DeventDefinition='<events><event><x>{0}</x><y>{1}</y></event></events>' -DfilePath={WSO2SIHome}/samples/artifacts/TimeExtensionSample/TimeSeries_events.txt -DnoOfEventsToSend=10 -DcontinuouslyReadFile=true
You can edit -DfilePath={WSO2SIHome}/samples/artifacts/TimeExtensionSample/TimeSeries_events.txt -DnoOfEventsToSend=10 -DcontinuouslyReadFile=true file and give your own value set to check the lengthTimeRegress.

## Viewing the Results:
See the output on timeSeriesExtensionSample console.
Expected output
INFO {io.siddhi.core.stream.output.sink.LogSink} - timeSeriesExtensionSample : logStream : Event{timestamp=1513592366610, data=[5000.0, 7000.0, 822.6805758329987, 0.0, 0.6129017613945887], isExpired=false}


```sql
@App:name('TimeSeriesExtensionSample')
@App:Description('Demonstrates how to use the siddhi-execution-timeseries with regress function.')


@Source(type = 'http', receiver.url='http://localhost:8006/productionStream', basic.auth.enabled='false',
    @map(type='xml'))
define stream ProductionStream (y double, x double);

@sink(type='log')
define stream logStream (y double, x double, stderr double, beta0 double, beta1 double);

from ProductionStream#timeseries:regress(10, 50, 0.95, y, x)
select *
insert into OutputStream;

from OutputStream
select *
insert into logStream;
```