

## Purpose:
	This Kalman Filter function uses measurements observed over time containing noise and other inaccuracies, and produces estimated values for the current measurement using Kalman algorithms

## Prerequisites:
* Save this sample. If there is no syntax error, the following messages would be shown on the console
	     * - Siddhi App KalmanFilterExecutionSample successfully deployed.

## Executing the Sample:
	1) Start the Siddhi application by clicking on 'Run'
	2) If the Siddhi application starts successfully, the following messages would be shown on the console
* KalmanFilterExecutionSample.siddhi - Started Successfully!

## Testing the Sample:
You can publish data event to the file, through event simulator
a) Open event simulator by clicking on the second icon or press Ctrl+Shift+I.
	b) In the Single Simulation tab of the panel, select values as follows:
* Siddhi App Name  : KalmanFilterExecutionSample
* Stream Name      : SweetProductionStream
c) Enter following values in the fields and send
## name: cake
## amount: 55.6
c) Enter following values in the fields and send
## name: cake
## amount: 20.5

## Viewing the Results:
Messages similar to the following would be shown on the console.
INFO {io.siddhi.core.query.processor.stream.LogStreamProcessor} - KalmanFilterExecutionSample: Event :, StreamEvent{ timestamp=1513378257580, beforeWindowData=null, onAfterWindowData=null, outputData=[55.6], type=CURRENT, next=null}
INFO {io.siddhi.core.query.processor.stream.LogStreamProcessor} - KalmanFilterExecutionSample: Event :, StreamEvent{ timestamp=1513378266843, beforeWindowData=null, onAfterWindowData=null, outputData=[38.05000877553216], type=CURRENT, next=null}


```sql
@App:name("KalmanFilterExecutionSample")

@App:description('Produces estimated values for the current measurement of amount using Kalman algorithms.')


define stream SweetProductionStream (name string, amount double);

@sink(type='log')
define stream EstimatedAmountStream(name string, estimatedAmount double);

from SweetProductionStream
select name, kf:kalmanFilter(amount) as estimatedAmount
insert into EstimatedAmountStream;
```