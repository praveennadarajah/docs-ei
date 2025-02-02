

## Purpose:
This application demonstrates how to perform RDBMS operations using Siddhi queries. The sample depicts a scenario in a sweet production factory. The sweet production details, such as the name of the raw material and amount used for production, can be stored using insertSweetProductionStream. The following streams can be used to search, delete, update, or upsert (update or insert) the existing data in the store:
Search - searchSweetProductionStream
insert - insertSweetProductionStream
delete - deleteSweetProductionStream
update - updateSweetProductionStream
update or insert - updateOrInsertSweetProductionStream
contains - containsSweetProductionStream (verifies whether all the attributes that enter in the stream exist in the store)

## Prerequisites:
1) Ensure that MySQL is installed on your machine.
2) Add the MySQL JDBC driver into {WSO2_SI_HOME}/lib as follows:
a) Download the JDBC driver from: https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-5.1.45.tar.gz
b) Unzip the archive.
c) Copy mysql-connector-java-5.1.45-bin.jar to {WSO2_SI_Home}/lib directory.
3) Create a database named 'production' in MySQL. This database is referred to with the 'jdbc:mysql://localhost:3306/production' url.
4) Create a table named 'SweetProductionTable': CREATE TABLE SweetProductionTable (name VARCHAR(20),amount double(10,2));
5) Insert some values into the table: INSERT INTO SweetProductionTable VALUES ('Sugar',23.50); 
## Note: You can also use insertSweetProductionStream for this.
6) In the store configuration of this application, replace 'username' and 'password' values with your MySQL credentials.
7) Save this sample.

## Executing the Sample:
1) Start the Siddhi application by clicking on 'Run'.
2) If the Siddhi application starts successfully, the following message is shown on the console
* Store-rdbms.siddhi - Started Successfully!

## Note:
If you want to edit this application while it's running, stop the application, make your edits and save the application, and then start it again. 

## Testing the Sample:
1) Simulate single events:
a) Click on 'Event Simulator' (double arrows on left tab) and click 'Single Simulation'
b) Select 'Store-rdbms' as 'Siddhi App Name' and select 'searchSweetProductionStream' as 'Stream Name'.
c) Provide attribute values, and then click Send.
2) Send at least one event where the name matches a name value in the data you previously inserted into the SweetProductionTable. This will satisfy the 'on' condition of the join query.
3) Optionally, send events to the other corresponding streams to add, delete, update, insert, and search events.

## Notes:
- After a change in the store, you can use the search stream to see whether the operation is successful.
- The Primary Key constraint in SweetProductionTable is disabled, because the name cannot be used as a PrimaryKey in a ProductionTable.
- You can use Siddhi functions to create a unique ID for the received events, which can then be used to apply the Primary Key constraint on the data store records. (http://wso2.github.io/siddhi/documentation/siddhi-4.0/#function)

## Viewing the Results:
See the output for raw materials on the console. You can use searchSweetProductionStream to check for inserted, deleted, and updated events.

*/
/* Inserting event into the mysql database */
@info(name='query1')
from insertSweetProductionStream
insert into SweetProductionTable;

/* Deleting event from mysql database */
/* Updating event from mysql database */
@info(name = 'query3')
from updateSweetProductionStream
update SweetProductionTable
on SweetProductionTable.name == name ;

/* Updating or inserting event from mysql database */
/* Siddhi In in mysql database */
@info(name = 'query5')
from containsSweetProductionStream
[(SweetProductionTable.name == name and SweetProductionTable.amount == amount) in SweetProductionTable]
insert into logStream;

--Perform a join on raw material name so that the data in the store can be viewed
@info(name='query6')
from searchSweetProductionStream as s join SweetProductionTable as sp
on s.name == sp.name
select sp.name, sp.amount
insert into logStrea
```sql
@App:name("Store-rdbms")
@App:description('Receive events via simulator and persist the received data in the store.')


define stream insertSweetProductionStream (name string, amount double);
define stream deleteSweetProductionStream (name string);
define stream searchSweetProductionStream (name string);
define stream updateSweetProductionStream (name string, amount double);
define stream updateOrInsertSweetProductionStream (name string, amount double);
define stream containsSweetProductionStream (name string, amount double);

@sink(type='log')
define stream logStream(name string, amount double);

@Store(type="rdbms",
       jdbc.url="jdbc:mysql://localhost:3306/production?useSSL=false",
       username="wso2",
       password="123" ,
       jdbc.driver.name="com.mysql.jdbc.Driver")
--@PrimaryKey("name")
@index("amount")
define table SweetProductionTable (name string, amount double);

@info(name = 'query2')
from deleteSweetProductionStream
delete SweetProductionTable
on SweetProductionTable.name == name ;

@info(name = 'query4')
from updateOrInsertSweetProductionStream
update or insert into SweetProductionTable
on SweetProductionTable.name == name;
```