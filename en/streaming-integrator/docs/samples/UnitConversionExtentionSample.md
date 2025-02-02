
## Purpose:
	This sample demonstrates how to use unit conversion extension for converting units.

## Prerequisites:
* Save this sample. If there is no syntax error, the following messages would be shown on the console
	     * - Siddhi App UnitConversionExtensionSample successfully deployed.

## Executing the Sample:
	1) Start the Siddhi application by clicking on 'Run'
	2) If the Siddhi application starts successfully, the following messages would be shown on the console
* UnitConversionExtensionSample.siddhi - Started Successfully!

## Testing the Sample:
You can publish data event to the file, through event simulator
a) Open event simulator by clicking on the second icon or press Ctrl+Shift+I.
	b) In the Single Simulation tab of the panel, select values as follows:
* Siddhi App Name  : UnitConversionExtensionSample
* Stream Name     : SweetProductionStream
c) Enter and send suitable values for the attributes of selected stream.


## Viewing the Results:
## Viewing the Results:
Messages similar to the following would be shown on the console.
INFO {io.siddhi.core.stream.output.sink.LogSink} - UnitConversionExtensionSample : WeightConvertedStream : Event{timestamp=1513588858315, data=[Chocolate, 1250.0], isExpired=false}


```sql
@App:name("UnitConversionExtensionSample")
@App:description('Demonstrates how to use unit conversion extension for converting units.')


define stream SweetProductionStream (name string, amount double);

@sink(type='log')
define stream WeightConvertedStream(name string, weightInGrams double);

from SweetProductionStream
select name, unitconversion:kgTog(amount) as  weightInGrams
insert into WeightConvertedStream;
```