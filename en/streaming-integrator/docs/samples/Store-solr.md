

## Purpose:
This application demonstrates how to perform CRUD operations using Siddhi queries in Solr stores. The sample depicts a scenario in a sweet production factory. The sweet production details such as name of the raw material, amount used for production can be stored using insertSweetProductionStream. The following streams can be used to search, delete, update or upsert(update or insert) the existing data in the store
Search - searchSweetProductionStream
insert - insertSweetProductionStream
delete - deleteSweetProductionStream
update - updateSweetProductionStream
update or insert - updateOrInsertSweetProductionStream
contains - containsSweetProductionStream (verifies whether all the attributes that enter in the stream exists in the store).


## Prerequisites:
(1) Download the solr-6.x.x.zip distribution from https://archive.apache.org/dist/lucene/solr/
(2) Start the Solr server in cloud mode using the command "{SOLR_HOME}/bin/solr -e cloud". This will create a simple solr cloud in your local machine.
When creating the cloud provide the suggested examples values for the each field.Give the collection name as gettingstarted. For the configuration provide "basic_configs".

## Executing the Sample:
1) Start the Siddhi application by clicking on 'Run'
2) If the Siddhi application starts successfully, the following messages would be shown on the console
* Store-solr.siddhi - Started Successfully!

## Testing the Sample:
1) Simulate single events. For this, click on 'Event Simulator' (double arrows on left tab) -> 'Single Simulation' -> Select 'Store-solr' as 'Siddhi App Name' -> Select 'searchSweetProductionStream' as 'Stream Name' -> Provide attribute values -> Send
2) Send at-least one event with the single event simulator, where the name matches a name value in the data we previously inserted to the SweetProductionTable. This would satisfy the 'on' condition of our join query.
3) Likewise the events can be sent to the other corresponding streams to add, delete, update, insert, search events.
4) After a change in the store, using the search stream the developer can see whether the operation is successful.
5) Primary Key constraint SweetProductionTable is disabled, since name cannot be used as a PrimaryKey in ProductionTable.
Siddhi functions can be used to create a unique id for the received events which can then be used to apply Primary Key constraint on the data store records. (http://wso2.github.io/siddhi/documentation/siddhi-4.0/#function)

## Viewing the Results:
See the output for row materials on the console. Inserted, deleted, updated events can be checked by searchSweetProductionStream.

*/
/* Inserting events*/
@info(name='query1')
from insertSweetProductionStream
insert into SweetProductionTable;

/* Deleting events*/
/* Updating events*/

@info(name = 'query3')
from updateSweetProductionStream
select name,amount
update SweetProductionTable
set SweetProductionTable.amount = amount
on SweetProductionTable.name == name ;

/* Updating or inserting events */
/* Siddhi In (Contains)*/
@info(name = 'query5')
from containsSweetProductionStream
[(SweetProductionTable.name == name and SweetProductionTable.amount == amount) in SweetProductionTable]
insert into logStream;

@info(name='query6')
from searchSweetProductionStream as s join SweetProductionTable as sp
on s.name == sp.name
select sp.name, sp.amount
insert into logStrea
```sql
@App:name("Store-solr")
@App:description('Receive events via simulator and received data are persisted in store.')


define stream insertSweetProductionStream (name string, amount double);
define stream deleteSweetProductionStream (name string);
define stream searchSweetProductionStream (name string);
define stream updateSweetProductionStream (name string, amount double);
define stream updateOrInsertSweetProductionStream (name string, amount double);
define stream containsSweetProductionStream (name string, amount double);

@sink(type='log')
define stream logStream(name string, amount double);

@Store(type='solr', collection='SweetProductionTable', zookeeper.url='localhost:9983', shards='2', replicas='2', schema='name string stored,amount double stored')
--@PrimaryKey("name")
@index("amount")
define table SweetProductionTable (name string, amount double);

@info(name = 'query2')
from deleteSweetProductionStream
delete SweetProductionTable
on SweetProductionTable.name == name ;


@info(name = 'query4')
from updateOrInsertSweetProductionStream
select name,amount
update or insert into SweetProductionTable
set SweetProductionTable.amount = amount
on SweetProductionTable.name == name;
```