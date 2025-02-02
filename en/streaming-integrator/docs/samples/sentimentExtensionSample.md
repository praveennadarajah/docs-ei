
## Purpose:
	This application demonstrates calculating the sentiment value for a given string as per Affin word list.

## Prerequisites:
	1) Save this sample.
	2) If there is no syntax error, the following message is shown on the console:
	       * - Siddhi App sentimentExtensionSample successfully deployed.

## Executing the Sample:
	1) Start the Siddhi application by clicking on 'Run'.
	2) If the Siddhi application starts successfully, the following messages are shown on the console:
	  * sentimentExtensionSample.siddhi - Started Successfully!

## Testing the Sample:
Send events through one or more of the following methods.

* You may send events to userWallPostStream, via event simulator
a) Open the event simulator by clicking on the second icon or pressing Ctrl+Shift+I.
b) In the Single Simulation tab of the panel, specify the values as follows:
* Siddhi App Name  : sentimentExtensionSample
* Stream Name      : userWallPostStream
c) In the userId and text fields, enter the following and then click Send to send the event.
## userId: Mohan",
## ext: "David is a good person. David is a bad person"
d) Send another event.
## userId: "Nuwan",
## ext:  "David is a good person."

* Send events to the simulator http endpoint through the curl commands:
a) Open a new terminal and issue the following commands one after the other:
curl -X POST \
## http://localhost:9390/simulation/single \
-u admin:admin \
-H 'content-type: text/plain' \
-d '{
"siddhiAppName": "sentimentExtensionSample",
"streamName": "userWallPostStream",
"timestamp": null,
"data": [
"Mohan",
"David is a good person. David is a bad person"
]
}'

curl -X POST \
## http://localhost:9390/simulation/single \
-u admin:admin \
-H 'content-type: text/plain' \
-d '{
"siddhiAppName": "sentimentExtensionSample",
"streamName": "userWallPostStream",
"timestamp": null,
"data": [
"Nuwan",
"David is a good person."
]
}'

b) If there is no error, the following messages are shown on the terminal:
*  {"status":"OK","message":"Single Event simulation started successfully"}

* Publish events with Postman:
a) Install 'Postman' application from Chrome web store.
b) Launch the application.
c) Make 'POST' request to the 'http://localhost:9390/simulation/single' endpoint. Set the Content-Type to 'text/plain'.
d) Set the following request body in text and send the first event.
	{"streamName": "userWallPostStream", "siddhiAppName": "sentimentExtensionSample",
	"data":  [
"Mohan",
"David is a good person. David is a bad person"
]}
d) If there are no errors, the following messages are shown on the console:
*  "status": "OK",
*  "message": "Single Event simulation started successfully"
e) Send another event by setting the request body in text
{"streamName": "userWallPostStream", "siddhiAppName": "sentimentExtensionSample",
"data":  [
"Nuwan",
"David is a good person."
]}

## Viewing the Results:
See the output on the terminal.
INFO {io.siddhi.core.stream.output.sink.LogSink} - sentimentExtensionSample : outputStream : Event{timestamp=1513619669217, data=[Mohan , David is a good person. David is a bad person, 0], isExpired=false}
INFO {io.siddhi.core.stream.output.sink.LogSink} - sentimentExtensionSample : outputStream : Event{timestamp=1513619687568, data=[Nuwan , David is a good person., 3], isExpired=false}

```sql
@APP:name('sentimentExtensionSample')
@App:Description('Demonstrates calculating the sentiment value for a given string as per Affin word list..')

-- Please refer to https://docs.wso2.com/display/SP400/Quick+Start+Guide on getting started with SP editor.

define stream userWallPostStream (userId string, text string);

@sink(type='log')
define stream outputStream(userId string, text string, rate int) ;

from userWallPostStream
select userId, text, sentiment:getRate(text) as rate
insert into outputStream;
```