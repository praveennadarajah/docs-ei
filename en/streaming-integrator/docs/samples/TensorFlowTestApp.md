
## Purpose:
	This application demonstrates how to import a pretrained Tensorflow model WSO2 Streaming Integrator to perform a regression task.

## Prerequisites:
1) Replace {SI_HOME} with absolute path to the Streaming Integrator Tooling home directory.
	2) Save this sample.
	3) If there is no syntax error, the following message is shown on the console:
	       * - Siddhi App TensorFlowTestApp successfully deployed.

## Executing the Sample:
	1) Start the Siddhi application by clicking on 'Run'.
	2) If the Siddhi application starts successfully, the following messages are shown on the console:
	  * TensorFlowTestApp.siddhi - Started Successfully!

## Testing the Sample:
Send events through one or more of the following methods.

* You may send events to ProductionInputStream, via event simulator
a) Open the event simulator by clicking on the second icon or pressing Ctrl+Shift+I.
b) In the Single Simulation tab of the panel, specify the values as follows:
* Siddhi App Name  : TensorFlowTestApp
* Stream Name      : InputStream
c) In the x field, enter the following and then click Send to send the event.
## x: "double:[1,-2]"

d) Send some more events.

* Send events to the simulator http endpoint through the curl command:
a) Open a new terminal and issue the following command:
* curl -X POST \http://localhost:9390/simulation/single \-H 'content-type: text/plain' \-d '{"streamName": "InputStream", "siddhiAppName": "TensorFlowTestApp","data": ["double:[1,-2]"]}'
b) If there is no error, the following messages are shown on the terminal:
*  {"status":"OK","message":"Single Event simulation started successfully"}

* Publish events with Postman:
a) Install 'Postman' application from Chrome web store.
b) Launch the application.
c) Make a 'Post' request to the 'http://localhost:9390/simulation/single' endpoint. Set the Content-Type to 'text/plain' and set the request body in text as follows:
	{"streamName": "InputStream", "siddhiAppName": "TensorFlowTestApp","data": ['double:[1,-2]']}
d) Click 'send'. If there is no error, the following messages are shown on the console:
*  "status": "OK",
*  "message": "Single Event simulation started successfully"

## Viewing the Results:
See the output on the terminal.

```sql
@App:name("TensorFlowTestApp")
define stream InputStream (x string);

@sink(type='log') 
define stream OutputStream (outputPoint0 double, outputPoint1 double);

@info(name = 'query1')
from InputStream#tensorFlow:predict('{SI_HOME}/samples/artifacts/TensorflowSample/Regression', 'inputPoint', 'outputPoint', x)
select outputPoint0, outputPoint1
insert into OutputStream;
```