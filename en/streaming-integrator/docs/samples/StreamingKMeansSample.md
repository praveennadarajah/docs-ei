
## Purpose:
	This sample demonstrates how to use siddhi-execution-streamingml kmeans incremental function for clustering.

## Prerequisites:
* Save this sample. If there is no syntax error, the following messages would be shown on the console
	     * - Siddhi App StreamingKMeansSample successfully deployed.

## Executing the Sample:
	1) Start the Siddhi application by clicking on 'Run'
	2) If the Siddhi application starts successfully, the following messages would be shown on the console
* StreamingKMeansSample.siddhi - Started Successfully!

## Testing the Sample:
You can publish data event to the file, through event simulator
a) Open event simulator by clicking on the second icon or press Ctrl+Shift+I.
	b) In the Single Simulation tab of the panel, select values as follows:
* Siddhi App Name  : StreamingKMeansSample
* Stream Name     : SweetProductionStream
c) Enter and send suitable values for the attributes of selected stream.

## Viewing the Results:
## Viewing the Results:
Messages similar to the following would be shown on the console.
INFO {io.siddhi.core.stream.output.sink.LogSink} - SteamingMLExtensionkmeans-incremental-sample : SweetStatePredictionStream : Event{timestamp=1513603080892, data=[12.5, 124.5, 12.5, 124.5], isExpired=false}

First two values of the data array represent the coordinates of the cluster that given product belongs to.
(eg: 12.5, 124.5)

```sql
@App:name("StreamingKMeansSample")
@App:Description('Demonstrates how to use siddhi-execution-streamingml kmeans incremental function for clustering.')



define stream SweetProductionStream(temperature double, density double);

@sink(type='log')
define stream SweetStatePredictionStream(closestCentroidCoordinate1 double, closestCentroidCoordinate2 double, temperature double, density double);

@info(name = 'query1')
from SweetProductionStream#streamingml:kMeansIncremental(2, 0.2, temperature, density)
select closestCentroidCoordinate1, closestCentroidCoordinate2, temperature, density
insert into SweetStatePredictionStream;
```