
## Purpose:
	This application demonstrates how to configure WSO2 Streaming Integrator Tooling to Publish and receive data events processed within Siddhi to files in CSV custom format.

## Prerequisites:
* Edit the uri '{WSO2SIHome}/samples/artifacts/CSVMappingWithFile/new/example.csv' by replacing {WSO2SIHome} with the absolute path of your WSO2SP home directory. You can also change the path for 'file.uri' in the sink, if you want to publish your event file to a different location.

	* Save this sample. If there is no syntax error, the following messages would be shown on the console:
	     * - CSVCustomMapping.siddhi successfully deployed.

Executing & testing the Sample:
	1) Start the Siddhi application by clicking on 'Run'.
	2) If the Siddhi application starts successfully, the following messages are shown on the console:
* CSVCustomMapping.siddhi - Started Successfully!

## Viewing the Results:
	Source takes input from the {WSO2SP-HOME}/samples/artifacts/CSVMappingWithFile/new/example.csv and produces the event.
	example.csv has data in below format 
			1,WSO2,23.5
			2,IBM,2.5
	Sink takes input from source output and produces the output to outputOfCustom.csv.
	outputOfCustom.csv's data appear in below format
			WSO2,1,100.0
			IBM,2,2.5
*
```sql
@App:name("CSVCustomMapping")
@App:description('Publish and receive data events processed within Siddhi to files in CSV custom format.')



@source(type='file',
dir.uri='file://{WSO2SIHome}/samples/artifacts/CSVMappingWithFile/new',
action.after.process='NONE',
@map(type='csv', @attributes(id='0', name='1', amount='2')))
define stream IntputStream (name string, id int,  amount double);

@sink(type='file', file.uri='/{WSO2SIHome}/samples/artifacts/CSVMappingWithFile/new/outputOfCustom.csv' , @map(type='csv',@payload(id='1', name='0', amount='2')))
define stream OutputStream (name string, id int, amount double);

from IntputStream
select *
insert into OutputStream;
```