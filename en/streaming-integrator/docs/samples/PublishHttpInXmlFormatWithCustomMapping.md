
## Purpose:
	This application demonstrates how to configure WSO2 Streaming Integrator to send sweet production events via HTTP transport in XML format using custom mapping. Map the input events($.item.id) to stream events(name) and log the events in LowProducitonAlertStream on the output console.

## Prerequisites:
	1) Save this sample. The following message appears on the console. 
	    * Siddhi App PublishHttpInXmlFormatWithCustomMapping successfully deployed. 

## Executing the Sample:
	1) Start the Siddhi application by clicking 'Run'.
	2) If the Siddhi application starts successfully, the following message appears on the console.
	* PublishHttpInXmlFormatWithCustomMapping.siddhi - Started Successfully!

	Notes:
	If you edit this application while it's running, save the application and then click 'Run'.
	If the message "'PublishHttpInXmlFormatWithCustomMapping.siddhi - Started Successfully!" does not appear, it could be due to port 8080, which is defined in the Siddhi application, already being used by a different program. To resolve this issue, please do the following:
	* Stop this Siddhi application (click 'Run' on the menu bar -> 'Stop').
	* Change the port 8080 to an unused port in this Siddhi application's source configuration.
	* Start the application and check whether the specified messages appear on the console.

## Testing the Sample:
1) Open a terminal and navigate to {WSO2SIHome}/samples/sample-clients/http-server and run "ant" command without any arguments.
2) Send events using one or more of the following methods.
* Send events with the HTTP server through the event simulator:
a) Open event simulator by clicking on the second icon or press Ctrl+Shift+I.
b) In the Single Simulation tab of the panel, select values as follows:
* Siddhi App Name: PublishHttpInXmlFormatWithCustomMapping
* Stream Name: SweetProductionStream
c) In the name field and amount fields, enter 'toffee' and '50.0' respectively. Then click Send to send the event.
d) Send some more events.

* Send events to the HTTP endpoint defined by 'publish.url' in the Sink configuration using the following cURL command:
a) Open a new terminal and issue the following command:
* curl -X POST -d '{"streamName": "SweetProductionStream", "siddhiAppName": "PublishHttpInXmlFormatWithCustomMapping","data": ['toffee', 67.43]}' http://localhost:9390/simulation/single -H 'content-type: text/plain'
b) If there is no error, the following messages are shown on the terminal:
*  {"status":"OK","message":"Single Event simulation started successfully"}

## Viewing the Results:
	* Ouput by sending events through the event simulator:
	[java] [org.wso2.si.http.server.HttpServerListener] : Event Name Arrived: <events><event><name>toffee</name><amount>50.0</amount></event></events>
[java] [org.wso2.si.http.server.HttpServerMain] : Received Event Names:<events><event><name>toffee</name><amount>50.0</amount></event></events> ,
[java] [org.wso2.si.http.server.HttpServerMain] : Received Event Headers key set:[Http_method, Transfer-encoding, Content-type]
[java] [org.wso2.si.http.server.HttpServerMain] : Received Event Headers value set:[[POST], [chunked], [application/xml]]


* Output by sending events through event cURL commands:
[java] [org.wso2.si.http.server.HttpServerListener] : Event Name Arrived: <events><event><name>toffee</name><amount>67.43</amount></event></events>
[java] [org.wso2.si.http.server.HttpServerMain] : Received Event Names:<events><event><name>toffee</name><amount>67.43</amount></event></events> ,
[java] [org.wso2.si.http.server.HttpServerMain] : Received Event Headers key set:[Http_method, Transfer-encoding, Content-type]
[java] [org.wso2.si.http.server.HttpServerMain] : Received Event Headers value set:[[POST], [chunked], [application/xml]]

	Note:
	Stop this Siddhi application once you are done with the execution.

```sql
@App:name("PublishHttpInXmlFormatWithCustomMapping")
@App:description('Send events via HTTP transport using xml formatwith custom mapping')



define stream SweetProductionStream (name string, amount double);

@sink(type='http', publisher.url='http://localhost:8080/abc',
@map(type='xml', @payload( "<StockData><Symbol>{{name}}</Symbol><Price>{{amount}}</Price></StockData>")))
define stream LowProductionAlertStream (name string, amount double);


from SweetProductionStream
select *
insert into LowProductionAlertStream;
```