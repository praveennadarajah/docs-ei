
## Purpose:
This sample demonstrates how to capture change data from MySQL using Siddhi.
INSERT, UPDATE, DELETE Change events can be captured.

Pre-requesites:
1) Ensure that MySQL is installed on your computer.
2) Add the MySQL JDBC driver into {WSO2_SI_HOME}/lib as follows:
2.1) Download the JDBC driver from: https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-5.1.45.tar.gz
2.2) Unzip the archive.
2.3) Copy mysql-connector-java-5.1.45-bin.jar to {WSO2_SI_Home}/lib directory.
3) Configure MySQL to enable binary logging.
4.1)Follow https://debezium.io/docs/connectors/mysql/#enabling-the-binlog
4) Enable state persistence in siddhi apps.
5.1)Change state.persistence enabled=true in deployment.yaml file.
5) Create a database "production".
>> CREATE SCHEMA production;
6) Make sure to have a user with SELECT, RELOAD, SHOW DATABASES, REPLICATION SLAVE, REPLICATION CLIENT privileges.
you can create a user with following.
>> GRANT SELECT, RELOAD, SHOW DATABASES, REPLICATION SLAVE, REPLICATION CLIENT ON *.* TO 'wso2sp' IDENTIFIED BY 'wso2';
7) Change the database.
>> use production;
8) Create table "SweetProductionTable".
>> CREATE TABLE SweetProductionTable (name VARCHAR(20),amount double(10,2));
9) Save this sample.

## Executing the Sample:
1) Start the Siddhi application by clicking on 'Run'.
2) If the Siddhi application starts successfully, the following message is shown on the console
* CDCWithListeningMode.siddhi - Started Successfully!
## Note:
If you want to edit this application while it's running, stop the application, make your edits and save the application, and then start it again. 

## Testing the Sample:
Insert data to the created table.
>> insert into SweetProductionTable values('chocolate',100.0);
Observe the inserted data is logged on the console.
## CDCWithListeningMode : logStream : Event{timestamp=1541486685789, data=[chocolate, 100.0], isExpired=false}

## Optional: Test for operation = 'update' and operation = 'delete' also.
## Note:   delete operation events will include keys: before_name, before_amount.
## update operation events will include keys: before_name, name, before_amount, amount.

```sql
@App:name('CDCWithListeningMode')
@App:description('Capture MySQL Inserts using cdc source listening mode.')


@source(type = 'cdc', url = 'jdbc:mysql://localhost:3306/production', username = 'wso2sp', password = 'wso2', table.name = 'SweetProductionTable', operation = 'insert', 
	@map(type = 'keyvalue'))
define stream insertSweetProductionStream (name string, amount double);

@sink(type = 'log')
define stream logStream (name string, amount double);

@info(name = 'query')
from insertSweetProductionStream
select name, amount
insert into logStream;
```