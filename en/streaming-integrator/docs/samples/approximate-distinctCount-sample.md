
## Purpose:
This application demonstrates how to calculate the number of distinct events within a Siddhi window with an accepted level of accuracy.

## Prerequisites:
1) Copy {WSO2SIHome}/samples/artifacts/ApproximateComputingSample/Test-approximate-distinctCount.json file to {WSO2_SI_Home}/wso2/editor/deployment/simulation-configs
2) Copy {WSO2SIHome}/samples/artifacts/ApproximateComputingSample/approximate-distinctCount.csv file to {WSO2_SI_Home}/wso2/editor/deployment/csv-files.

## Executing the Sample:
1) Start the Siddhi application by clicking on 'Run'
2) If the Siddhi application starts successfully, the following messages would be shown on the console
* approximate-distinctCount-sample.siddhi - Started Successfully!.

## Testing the Sample:
1) Click on 'Event Simulator' (double arrows on left tab)
2) Click 'Feed Simulation'
3) Select Test-approximate-distinctCount under 'Active Feed Simulations'
4) Click on the start button (Arrow symbol) next to the simulator

## Viewing the Results:
See the output on the console.

```sql
@App:name("approximate-distinctCount-sample")
@App:description('Calculate the number of distinct events within a Siddhi window with an accepted level of accuracy.')

@info(name = 'query')
define stream sensorStream (sensorId int);

@sink(type='log')
define stream OutputStream(distinctCount long, distinctCountLowerBound long, distinctCountUpperBound long);

from sensorStream#window.length(100)#approximate:distinctCount(sensorId, 0.01, 0.95)
select distinctCount, distinctCountLowerBound, distinctCountUpperBound
insert into OutputStream;
```