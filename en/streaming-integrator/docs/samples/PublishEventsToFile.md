

## Purpose:
	This application demonstrates how to configure WSO2 Streaming Integrator Tooling to publish data events in to files in Json format.

## Prerequisites:
* Edit the uri '{WSO2SIHome}/samples/artifacts/PublishEventsToFile/files/sink/{{name}}.txt' by replacing {WSO2SIHome} with the absolute path of your WSO2SP home directory. You can also change the path for 'file.uri' in the sink, if you want to publish your event file to a different location.

	* Save this sample. If there is no syntax error, the following messages would be shown on the console:
	     * - Siddhi App PublishEventsToFile successfully deployed.

## Executing the Sample:
	1) Start the Siddhi application by clicking on 'Run'.
	2) If the Siddhi application starts successfully, the following messages are shown on the console:
* PublishEventsToFile.siddhi - Started Successfully!

## Testing the Sample:
## You can publish data events to the file through the event simulator:
1) Click on 'Event Simulator' (double arrows on left tab)
2) Click 'Single Simulation' (this will be already selected)
3) Select PublishEventsToFile as 'Siddhi App Name'
4) Select SweetProductionStream as 'StreamName'
5) Click on the start button (Arrow symbol) next to the newly created simulator
6) Provide attribute values
- name: toffees
- amount: 66.71
7) Send event
8) Provide attribute values
- name: toffees
- amount: 200
9) Send event

## Viewing the Results:
Navigate to the path defined by file.uri ({WSO2SIHome}/samples/artifacts/0038/files/sink),
where you can see a .txt file named after the event (e.g., toffees.txt) and open it.
## You can see the data events that you sent:
{"event":{"name":"toffees","amount":66.71}}
{"event":{"name":"toffees","amount":200.0}}

```sql
@App:name("PublishEventsToFile")

@App:description('Publish data events processed within Siddhi to files in Json format.')


define stream SweetProductionStream (name string, amount double);

@sink(type='file', @map(type='json'), file.uri='{WSO2SIHome}/samples/artifacts/PublishEventsToFile/files/sink/{{name}}.txt')
define stream LowProductionAlertStream (name string, amount double);

@info(name='query1') 
from SweetProductionStream
select *
insert into LowProductionAlertStream;
```