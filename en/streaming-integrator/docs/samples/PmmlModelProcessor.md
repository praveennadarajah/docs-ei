
## Purpose:
This application demonstrates how to configure a pretrained PMML model to predict required raw materials from sweet production events and view the output on the console.

## Prerequisites:
1) Download siddhi-gpl-execution-pmml-x.x.x.jar from the following link and copy the jar to  {WSO2SIHome}/lib
## http://maven.wso2.org/nexus/content/repositories/wso2gpl/org/wso2/extension/siddhi/gpl/execution/pmml/siddhi-gpl-execution-pmml/
2) Save this sample.
3) If there is no syntax error, the following message is shown on the console:
	       * - Siddhi App PmmlModelProcessor successfully deployed.

## Executing the Sample:
1) Replace <SweetProductionRatePrediction_model_path> with the SweetProductionRatePrediction.pmml file's absolute path - {WSO2SIHome}/samples/artifacts/PMMLModelProcessorSample/SweetProductionRatePrediction.pmml
2) Start the Siddhi application by clicking on 'Run'.
3) If the Siddhi application starts successfully, the following messages would be shown on the console.
* PmmlModelProcessor.siddhi - Started Successfully!

## Notes:
If you edit this application while it's running, stop the application -> Save -> Start.
* Stop this Siddhi application (Click 'Run' on menu bar -> 'Stop')
* Start the application and check whether the specified events from the jms provider appear on the console.

## Testing the Sample:
Send events through one or more of the following methods.
* You may send events to SweetProductionStream, via event simulator:
a) Open the event simulator by clicking on the second icon or pressing Ctrl+Shift+I.
	       b) In the Single Simulation tab of the panel, specify the values as follows:
* Siddhi App Name  : PmmlModelProcessor
* Stream Name     : SweetProductionStream
c) In the name and amount fields, enter 'candy', 20, 23 respectively and then click Send to send the event.
d) Send more events as desired.

* Send events to the http endpoint defined by 'publish.url' in the Sink configuration through the curl command:
a) Open a new terminal and issue the following command:
*  curl -X POST -d '{"streamName": "SweetProductionStream", "siddhiAppName": "PmmlModelProcessor","data": ['candy', 20, 23]}' http://localhost:9390/simulation/single -H 'content-type: text/plain'
b) If there is no error, the following messages are shown on the terminal:
*  {"status":"OK","message":"Single Event simulation started successfully"}

* Publish events with Postman:
a) Install 'Postman' application from Chrome web store.
b) Launch the application.
c) Make a 'Post' request to the 'http://localhost:9390/simulation/single' endpoint. Set the Content-Type to 'text/plain' and set the request body in text as follows:
	{"streamName": "SweetProductionStream", "siddhiAppName": "PmmlModelProcessor","data": ['candy', 20, 23]}
d) Click 'send'. If there is no error, the following messages are shown on the console:
*  "status": "OK",
*  "message": "Single Event simulation started successfully"

## Notes:
* Accepted values for the 'name' field should be either candy, caramel-bar, peanut-butter-cup or truffle for the specific pretrained PMML model.

## Viewing the Results:
See the prediction as output on the in the console.

## Note:
Stop this Siddhi application, once you are done with the execution.

-- Please refer to https://docs.wso2.com/display/SP400/Quick+Start+Guide on getting started with SP editor.

```sql
@App:name("PmmlModelProcessor")
@App:description('Use a pretrained PMML model to predict required raw materials. View the output on the console.')
@Source(type = 'tcp', context='SweetProductionStream',
@map(type='binary'))
define stream SweetProductionStream (name string, currentHourAmount  double, previousHourAmount double );

@sink(type='log')
define stream PredictionStream (name string, currentHourAmount double, previousHourAmount double, Predicted_nextHourAmount string);

from SweetProductionStream#pmml:predict('<SweetProductionRatePrediction_model_path>')
select *
insert into PredictionStream;
```