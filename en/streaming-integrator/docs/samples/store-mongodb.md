
## Purpose:
This application demonstrates how to perform CRUD operations using Siddhi queries in MongoDB stores. The sample depicts a scenario in a sweet production factory. The sweet production details, such as name of the raw material and amount used for production, can be stored using insertSweetProductionStream. The following streams can be used to insert, update, search, delete and update or insert the existing data in the store.
Search - searchSweetProductionStream
insert - insertSweetProductionStream
delete - deleteSweetProductionStream
update - updateSweetProductionStream
update or insert - updateOrInsertSweetProductionStream
contains - containsSweetProductionStream (verifies whether all the attributes that enter in the stream exists in the store).

## Prerequisites:
1) Ensure that MongoDB is installed on your machine. (https://docs.mongodb.com/manual/administration/install-community/).
2) If auth is not enabled in the MongoDB instance, skip steps 3 and 4.
3) Create a data store named 'production' in MongoD with relevant access privileges.
4) Create a collection named 'SweetProductionTable'and insert values into 'SweetProductionTable'.
5) Save this sample.

## Executing the Sample:
1) Start the Siddhi application by clicking on 'Run'.
2) If the Siddhi application starts successfully, the following message is shown on the console:
* store-mongodb.siddhi - Started Successfully!

## Note:
If you want to edit this application, stop the application, make your edits and save, and then start the application again.

## Testing the Sample:
1) Simulate single events as follows: 
a) Click on Event Simulator (double arrows on left tab) and then 'Single Simulation'.
b) For the Siddhi App Name, select 'store-mongodb'.
c) For the Stream Name, select 'searchSweetProductionStream'.
d) Enter attribute values and click Send.
2) Send an event where the name matches a name value in the data you just inserted to the SweetProductionTable. This will satisfy the 'on' condition of the join query.

## Notes: 
* You can send events to the other corresponding streams to add, delete, update, insert, and search events.
* The Primary Key constraint in SweetProductionTable is disabled, because the name cannot be used as a PrimaryKey in a ProductionTable.
* You can use Siddhi functions to create a unique ID for the received events, which you can then use to apply the Primary Key constraint on the data store records. (http://wso2.github.io/siddhi/documentation/siddhi-4.0/#function)

## Viewing the Results:
See the output for raw materials on the console. You can use searchSweetProductionStream to check for inserted, deleted, and updated events.
*/
/* Inserting event into the mongo store */
@info(name='query1')
from insertSweetProductionStream
insert into SweetProductionTable;

/* Deleting event from mongo store */
/* Updating event from mongo store */
@info(name = 'query3')
from updateSweetProductionStream
update SweetProductionTable 
on SweetProductionTable.name == name ;

/* Updating or inserting event from mongo store */
/* Siddhi In in mongo store */
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
@App:name("store-mongodb.siddhi")


@App:description('Receive events via simulator and persist the received data in the store.')
define stream insertSweetProductionStream (name string, amount double);
define stream deleteSweetProductionStream (name string);
define stream searchSweetProductionStream (name string);
define stream updateSweetProductionStream (name string, amount double);
define stream updateOrInsertSweetProductionStream (name string, amount double);
define stream containsSweetProductionStream (name string, amount double);

@sink(type='log')
define stream logStream(name string, amount double);

@Store(type="mongodb",mongodb.uri='mongodb://localhost/production')
--@PrimaryKey("name")
@IndexBy("amount {background:true}")
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