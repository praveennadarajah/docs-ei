
## Purpose:
This sample demonstrates how to use polling mode of cdc source.
A MySQL table's INSERTS are captured.
By changing the database type, following databases wil also be supported.
* Oracle
* H2
* MS SQL Server
* Postgresql

Pre-requisites:
1) Ensure that MySQL is installed on your computer.
2) Add the MySQL JDBC driver into {WSO2_SI_HOME}/lib as follows:
2.1) Download the JDBC driver from: https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-5.1.45.tar.gz
2.2) Unzip the archive.
2.3) Copy mysql-connector-java-5.1.45-bin.jar to {WSO2_SI_Home}/lib directory.
3) Create a database "production".
>> CREATE SCHEMA production;
4) Change the database.
>> use production;
5) Create table "SweetProductionTable".
>> CREATE TABLE SweetProductionTable (last_update TIMESTAMP, name VARCHAR(20),amount double(10,2));
6) Save this sample.

Optional pre-requisites:
1) Enable state persistence in siddhi apps.
This will enable the cdc polling mode capturing changes from the last point it stopped.
If not enabled, polling mode will only capture changes after start of the app.
1.1) Change state.persistence enabled=true in deployment.yaml file.

## Executing the Sample:
1) Start the Siddhi application by clicking on 'Run'.
2) If the Siddhi application starts successfully, the following message is shown on the console
* CDCWithPollingMode.siddhi - Started Successfully!

## Note:
If you want to edit this application while it's running, stop the application, make your edits and save the application, and then start it again.

## Testing the Sample:
Insert data to the created table.
>> insert into SweetProductionTable(name,amount) values('chocolate',100.0);
Observe the inserted data is logged on the console.
## CDCWithPollingMode : logStream : Event{timestamp=1543830365138, data=[chocolate, 100.0], isExpired=false}

## Optional: Update existing row and obverse the change data events

## Note: For updates, the previous values of the row are not returned with the event. Use listening mode to obtain such details.
Polling mode is can also be used with Oracle, MS-SQL server, Postgres, H2.


```sql
@App:name("CDCWithPollingMode")
@App:description("Capture MySQL Inserts and Updates using cdc source polling mode.")


@source(type = 'cdc',
	url = 'jdbc:mysql://localhost:3306/production?useSSL=false',
	mode = 'polling',
	jdbc.driver.name = 'com.mysql.jdbc.Driver',
	polling.column = 'last_update',
	polling.interval = '1',
	username = '',
	password = '',
	table.name = 'SweetProductionTable',
	@map(type = 'keyvalue' ))
define stream insertSweetProductionStream (name string, amount double);

@sink(type = 'log')
define stream logStream (name string, amount double);

@info(name = 'query')
from insertSweetProductionStream
select name, amount
insert into logStream;
```