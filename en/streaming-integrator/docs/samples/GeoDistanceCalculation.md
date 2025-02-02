
## Purpose:

	This app will demonstrate how to use the extension siddhi-gpl-execution-geo to calculate distance between two locations.

## Prerequisites:
1. Download siddhi-gpl-execution-geo-x.x.x.jar from the following link and copy the jar to  {WSO2SIHome}/lib
- http://maven.wso2.org/nexus/content/repositories/wso2gpl/org/wso2/extension/siddhi/gpl/execution/geo/siddhi-gpl-execution-geo/

2. Save this sample. If there is no syntax error, the following messages would be shown on the console
	     * - Siddhi App GeoDistanceCalculation.siddhi successfully deployed.

## Executing the Sample:
1) Start the Siddhi application by clicking on 'Run'
	2) If the Siddhi application starts successfully, the following messages would be shown on the console
* GeoDistanceCalculation.siddhi - Started Successfully!

## Testing the Sample:
1) Click on 'Event Simulator' (double arrows on left tab)
2) Click 'Single Simulation' (this will be already selected)
3) Select GeoDistanceCalculation as 'Siddhi App Name'
4) Select LocationPointsStream as 'StreamName'
5) Click on the start button (Arrow symbol) next to the newly created simulator
6) Provide attribute values
- latitude : 8.116553
- longitude : 77.523679
- prevLatitude : 9.850047
- prevLongitude : 98.597177
7) Send event

## Viewing the Results:
After clicking the start button see the output on the console as below.
INFO {io.siddhi.core.query.processor.stream.LogStreamProcessor} - GeoDistanceCalculation: Event :, StreamEvent{ timestamp=1513616078228, beforeWindowData=null, onAfterWindowData=null, outputData=[2322119.848252557], type=CURRENT, next=null}


```sql
@App:name("GeoDistanceCalculation")

@App:description('This will demonstrate the distance between two locations')

define stream LocationPointsStream (latitude double, longitude double, prevLatitude double, prevLongitude double);

@sink(type='log')
define stream DistanceStream (distance double);

@info(name = 'query1')
from LocationPointsStream
select geo:distance(latitude, longitude, prevLatitude, prevLongitude) as distance
insert into DistanceStream;
```