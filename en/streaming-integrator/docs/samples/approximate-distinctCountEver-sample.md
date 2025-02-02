
## Purpose:
This application demonstrates how to calculate the number of distinct events based on a specific relative error. Returns erroneous values if is is used with a Siddhi window.

## Prerequisites:
1) Copy {WSO2SIHome}/samples/artifacts/ApproximateComputingSample/Test-approximate-distinctCountEver.json file to {WSO2_SI_Home}/wso2/editor/deployment/simulation-configs
2) Copy {WSO2SIHome}/samples/artifacts/ApproximateComputingSample/approximate-distinctCountEver.csv file to {WSO2_SI_Home}/wso2/editor/deployment/csv-files.

## Executing the Sample:
1) Start the Siddhi application by clicking on 'Run'
2) If the Siddhi application starts successfully, the following messages would be shown on the console
* approximate-distinctCountEver-sample.siddhi - Started Successfully!.

## Testing the Sample:
1) Click on 'Event Simulator' (double arrows on left tab)
2) Click 'Feed Simulation'
3) Select Test-approximate-distinctCountEver under 'Active Feed Simulations'
4) Click on the start button (Arrow symbol) next to the simulator

## Viewing the Results:
See the output on the console.

```sql
@App:name("approximate-distinctCountEver-sample")
@App:description('Calculate the number of distinct events without a Siddhi window.')
define stream requestStream (ip string);

@sink(type='log')
define stream OutputStream(distinctCountEver long, distinctCountEverLowerBound long, distinctCountEverUpperBound long);

@info(name = 'query')
from requestStream#approximate:distinctCountEver(ip)
select distinctCountEver, distinctCountEverLowerBound, distinctCountEverUpperBound
insert into OutputStream;
```