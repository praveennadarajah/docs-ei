

## Purpose:
	Through this app, NamedEntity of given type "organization" is extracted from the provided string.

## Prerequisites:
1. Download siddhi-gpl-execution-nlp-x.x.x.jar from the following link and copy the jar to  {WSO2SIHome}/lib
## http://maven.wso2.org/nexus/content/repositories/wso2gpl/org/wso2/extension/siddhi/gpl/execution/nlp/siddhi-gpl-execution-nlp/
2. Save this sample. If there is no syntax error, the following messages would be shown on the console
	     * - Siddhi App GplNLPFindNameEntityType.siddhi successfully deployed.

## Executing the Sample:
	1) Start the Siddhi application by clicking on 'Run'
	2) If the Siddhi application starts successfully, the following messages would be shown on the console
* GplNLPFindNameEntityType.siddhi - Started Successfully!

## Testing the Sample:
You can publish data event to the file, through event simulator
a) Open event simulator by clicking on the second icon or press Ctrl+Shift+I.
	b) In the Single Simulation tab of the panel, select values as follows:
* Siddhi App Name  : GplNLPFindNameEntityType
* Stream Name     : InputStream
c) Enter following string in the message and send
ABC factory produces 20 donuts per day.

## Viewing the Results:
## Viewing the Results:
Messages similar to the following would be shown on the console.
INFO {io.siddhi.core.query.processor.stream.LogStreamProcessor} - GplNLPFindNameEntityType: StreamEvent{ timestamp=1513573692406, beforeWindowData=null, onAfterWindowData=null, outputData=[ABC factory produces 20 donuts per day., ABC], type=CURRENT, next=null}
ABC has been recognized as an organization.

## Note:
1 Note:
Stop this Siddhi application, once you are done with the execution


```sql
@App:name('GplNLPFindNameEntityType')
@App:Description("NamedEntity of given type 'organization' is extracted from the provided string")


define stream InputStream (message string);

@sink(type='log')
define stream outputStream (message string, match string);

from InputStream#nlp:findNameEntityType( 'ORGANIZATION', true, message )
select *
insert into FindNameEntityTypeResult;

from FindNameEntityTypeResult
select *
insert into outputStream;
```