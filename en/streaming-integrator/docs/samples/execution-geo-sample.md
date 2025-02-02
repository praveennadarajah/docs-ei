
## Purpose:
This application demonstrates how to retrieve the longitude and latitude for the provided location details.

## Prerequisites:
1) Save this sample.
2) If there is no syntax error, the following message is shown on the console:
	       * - Siddhi App execution-geo-sample successfully deployed.

## Executing the Sample:
1) Start the Siddhi application by clicking on 'Run'.
2) If the Siddhi application starts successfully, the following messages would be shown on the console.
* execution-geo-sample.siddhi - Started Successfully!

## Testing the Sample:
1) Click on 'Event Simulator' (double arrows on left tab)
2) Click 'Single Simulation' (this will be already selected)
3) Select execution-geo-sample as 'Siddhi App Name'
4) Select geocodeStream as 'StreamName'
5) Click on the start button (Arrow symbol) next to the newly created simulator
6) Provide attribute values
- location: 5 Avenue Anatole France
- level: 75007 Paris
- time: France
7) Send event

## Viewing the Results:
See the prediction as output on the in the console.
INFO {io.siddhi.core.query.processor.stream.LogStreamProcessor} - sentimentExtensionSample: Event :, StreamEvent{ timestamp=1513623526790, beforeWindowData=null, onAfterWindowData=null, outputData=[48.8583698, 2.2944833, Tour Eiffel, 5 Avenue Anatole France, 75007 Paris, France], type=CURRENT, next=null}

```sql
@App:name("execution-geo-sample")
@App:description('Use geo data related functionality to retrieve the longitude and latitude for the provided location details.')

-- Please refer to https://docs.wso2.com/display/SP400/Quick+Start+Guide on getting started with streaming-integrator-tooling.

define stream geocodeStream (location string, level string, time string);

@sink(type='log')
define stream dataOut(latitude double, longitude double, formattedAddress string);

@info(name = 'query')
from geocodeStream#geo:geocode(location)
select latitude, longitude, formattedAddress
insert into dataOut;
```