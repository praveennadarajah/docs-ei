

## Purpose:
	This function returns the smallest (closest to negative infinity) double value that is greater than or equal to the
	 p1 argument, and is equal to a mathematical integer. This function wraps thejava.lang.Math.ceil() method.

## Prerequisites:
* Save this sample. If there is no syntax error, the following messages would be shown on the console
	     * - Siddhi App MathExtensionSample successfully deployed.

## Executing the Sample:
	1) Start the Siddhi application by clicking on 'Run'
	2) If the Siddhi application starts successfully, the following messages would be shown on the console
* MathExtensionSample.siddhi - Started Successfully!

## Testing the Sample:
You can publish data event to the file, through event simulator
a) Open event simulator by clicking on the second icon or press Ctrl+Shift+I.
	b) In the Single Simulation tab of the panel, select values as follows:
* Siddhi App Name  : MathExtensionSample
* Stream Name      : SweetProductionStream
c) Enter following values in the fields and send
## name: chocolate cake
## amount: 50.50
d) Enter following values in the fields and send
## name: coffee cake
## amount: 50.30


## Viewing the Results:
Messages similar to the following would be shown on the console.
INFO {io.siddhi.core.query.processor.stream.LogStreamProcessor} - MathExtensionSample: Event :, StreamEvent{ timestamp=1513381581963, beforeWindowData=null, onAfterWindowData=null, outputData=[chocolate cake, 51.0], type=CURRENT, next=null}
INFO {io.siddhi.core.query.processor.stream.LogStreamProcessor} - MathExtensionSample: Event :, StreamEvent{ timestamp=1513381917721, beforeWindowData=null, onAfterWindowData=null, outputData=[chocolate cake, 51.0], type=CURRENT, next=null}



```sql
@App:name("MathExtensionSample")

@App:description('Rounds up the sweets amount')


define stream SweetProductionStream (name string, amount double);

@sink(type='log')
define stream AmountCorrectionStream(name string, amount double);

from SweetProductionStream
select name, math:ceil(amount) as amount
insert into AmountCorrectionStream;
```