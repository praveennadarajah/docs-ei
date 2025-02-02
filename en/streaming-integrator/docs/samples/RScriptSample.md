
## Purpose:
This application demonstrates how to configure a Siddhi app to process events with a R script.

## Prerequisites:
1) Install R. See notes for more information see 'How to use
' in 'https://github.com/wso2-extensions/siddhi-gpl-execution-r/blob/master/README.md'
2) Download siddhi-gpl-execution-r-x.x.x.jar and copy the jar to  {WSO2SIHome}/lib
## http://maven.wso2.org/nexus/content/repositories/wso2gpl/org/wso2/extension/siddhi/gpl/execution/r/siddhi-gpl-execution-r/
3) Save this sample.
4) If there is no syntax error, the following message is shown on the console:
	       * - Siddhi App RScriptSample successfully deployed.

## Executing the Sample:
1) Start the Siddhi application by clicking on 'Run'.
2) If the Siddhi application starts successfully, the following messages would be shown on the console.
* RScriptSample.siddhi - Started Successfully!

## Testing the Sample:
Send events through one or more of the following methods.
* You may send events to weather stream, via event simulator:
a) Open the event simulator by clicking on the second icon or pressing Ctrl+Shift+I.
	       b) In the Single Simulation tab of the panel, specify the values as follows:
* Siddhi App Name  : RScriptSample
* Stream Name     : weather
c) Use the following values in the time and temp fields and send each value pair one after the other.
## ime: 10, temp:55.6
## ime: 20, temp:65.6
## ime: 30, temp:75.6

## Viewing the Results:
See the processed output for each event on the console.

-- Please refer to https://docs.wso2.com/display/SP400/Quick+Start+Guide on getting started with SP editor.

```sql
@App:name("RScriptSample")
@App:description('Use a R script to process events and produce aggregated outputs based on the provided input variable parameters and expected output attributes.')

define stream weather (time long, temp double);

@sink(type='log')
define stream dataOut (time long, temp double, c long, m double );

@info(name = 'query')
from weather#window.lengthBatch(2)#r:eval("c <- sum(time); m <- sum(temp); ", "c long, m double",time, temp)
select *
insert into dataOut;
```