
## Purpose:
This application demonstrates how to train a Hoeffding Classifier and to predict the sweet category from the sweet production stream in streaming manner.

## Prerequisites:
1. Download siddhi-gpl-execution-streamingml-x.x.x.jar from the following link and copy the jar to  {WSO2SIHome}/lib
## http://maven.wso2.org/nexus/content/repositories/wso2gpl/org/wso2/extension/siddhi/gpl/execution/streamingml/siddhi-gpl-execution-streamingml/
2) Save this sample.
3) If there is no syntax error, the following message is shown on the console:
	       * - Siddhi App streaming-hoeffding-classifier-sample successfully deployed.

## Executing the Sample:
1) Start the Siddhi application by clicking on 'Run'.
2) If the Siddhi application starts successfully, the following messages would be shown on the console.
* streaming-hoeffding-classifier-sample.siddhi - Started Successfully!

## Notes:
If you edit this application while it's running, stop the application -> Save -> Start.
* Stop this Siddhi application (Click 'Run' on menu bar -> 'Stop')
* Start the application and check whether the specified events from the jms provider appear on the console.

## Testing the Sample:

## Note:
* The Hoeffding Classifier for streaming machine learning needs to be trained prior to perform prediction.

1) Training phase
Send events through one or more of the following methods.

* You may send events to ProductionTrainStream, via event simulator
a) Open the event simulator by clicking on the second icon or pressing Ctrl+Shift+I.
b) In the Single Simulation tab of the panel, specify the values as follows:
* Siddhi App Name  : streaming-hoeffding-classifier-sample
* Stream Name      : ProductionTrainStream
c) In the name and amount fields, enter the following and then click Send to send the event.
## density: 50.4
## solubility: 30.03
## sweetType: candy

d) Send more events including upto 3 unique sweet types as specified as the parameter during the training phase.
@info(name = 'query-train')
from ProductionTrainStream#streamingml:updateHoeffdingTree('classifierModel', 3, density, solubility, sweetType )

* Send events to the simulator http endpoint through the curl command:
a) Open a new terminal and issue the following command:
* curl -X POST -d '{"streamName": "ProductionTrainStream", "siddhiAppName": "streaming-hoeffding-classifier-sample","data": [50.4, 30.03, candy]}' http://localhost:9390/simulation/single -H 'content-type: text/plain'
b) If there is no error, the following messages are shown on the terminal:
*  {"status":"OK","message":"Single Event simulation started successfully"}

* Publish events with Postman:
a) Install 'Postman' application from Chrome web store.
b) Launch the application.
c) Make a 'Post' request to the 'http://localhost:9390/simulation/single' endpoint. Set the Content-Type to 'text/plain' and set the request body in text as follows:
	{"streamName": "ProductionTrainStream", "siddhiAppName": "streaming-hoeffding-classifier-sample","data": [50.4, 30.03, candy]}
d) Click 'send'. If there is no error, the following messages are shown on the console:
*  "status": "OK",
*  "message": "Single Event simulation started successfully"
2) Testing phase
Send events through one or more of the following methods.

* You may send events to ProductionInputStream, via event simulator
a) Open the event simulator by clicking on the second icon or pressing Ctrl+Shift+I.
b) In the Single Simulation tab of the panel, specify the values as follows:
* Siddhi App Name  : streaming-hoeffding-classifier-sample
* Stream Name      : SweetProductionStream
c) In the name and amount fields, enter the following and then click Send to send the event.
## density: 30.4
## emperature: 20.5

* Send events to the simulator http endpoint through the curl command:
a) Open a new terminal and issue the following command:
* curl -X POST -d '{"streamName": "SweetProductionStream", "siddhiAppName": "streaming-hoeffding-classifier-sample","data": [30.4, 20.5]}' http://localhost:9390/simulation/single -H 'content-type: text/plain'
b) If there is no error, the following messages are shown on the terminal:
*  {"status":"OK","message":"Single Event simulation started successfully"}

* Publish events with Postman:
a) Install 'Postman' application from Chrome web store.
b) Launch the application.
c) Make a 'Post' request to the 'http://localhost:9390/simulation/single' endpoint. Set the Content-Type to 'text/plain' and set the request body in text as follows:
	{"streamName": "SweetProductionStream", "siddhiAppName": "streaming-hoeffding-classifier-sample","data": [30.4, 20.5]}
d) Click 'send'. If there is no error, the following messages are shown on the console:
*  "status": "OK",
*  "message": "Single Event simulation started successfully"

## Viewing the Results:
## See the output on the terminal:
INFO {io.siddhi.core.stream.output.sink.LogSink} - streaming-hoeffding-classifier-sample : PredictionStream : Event{timestamp=1513610806272, data=[30.4, 20.5, candy, 0.0], isExpired=false}
-- Please refer to https://docs.wso2.com/display/SP400/Quick+Start+Guide on getting started with SP editor.

```sql
@App:name("streaming-hoeffding-classifier-sample")
@App:description('Train a streaming Hoeffding Classifier and to predict the type of sweet.')

define stream ProductionTrainStream (density double, solubility double, sweetType string );

define stream SweetProductionStream (density double, solubility double);

@sink(type='log')
define stream PredictionStream (density double, solubility double, prediction string, confidenceLevel double);

@info(name = 'query-train')
from ProductionTrainStream#streamingml:updateHoeffdingTree('classifierModel', 3, density, solubility, sweetType )
select *
insert into trainOutputStream;

@info(name = 'query-predict')
from SweetProductionStream#streamingml:hoeffdingTreeClassifier('classifierModel', density, solubility )
select density, solubility, prediction, confidenceLevel
insert into PredictionStream;
```