
## Purpose:
This application demonstrates how to perform CRUD operations in HBase stores using Siddhi queries. The sample depicts a scenario in a sweet production factory. The sweet production details, such as name of the raw material and amount used for production, can be stored using insertSweetProductionStream. The following streams can be used to search, delete, update, or upsert (update or insert) the existing data in the store.
Search - searchSweetProductionStream
insert - insertSweetProductionStream
delete - deleteSweetProductionStream
update - updateSweetProductionStream
update or insert - updateOrInsertSweetProductionStream
contains - containsSweetProductionStream (verifies whether all the attributes that enter in the stream exists in the store).

## Prerequisites:
1) Ensure that HBase is installed and the server is running on your machine.
2) Add HBase client into {WSO2_SI_HOME}/lib as follows:
a) Download the following jars:
* HBase client (http://central.maven.org/maven2/org/apache/hbase/hbase-shaded-client/1.3.1/hbase-shaded-client-1.3.1.jar)
* Apache HTrace core (http://central.maven.org/maven2/org/apache/htrace/htrace-core/3.1.0-incubating/htrace-core-3.1.0-incubating.jar)
b) Use the "jartobundle" tool in {WSO2_SI_Home}/bin to convert the above jar into a OSGi bundle.
## For Windows: <SI_HOME>/bin/jartobundle.bat <PATH_OF_DOWNLOADED_JAR> <PATH_OF_CONVERTED_JAR>
## For Linux: <SI_HOME>/bin/jartobundle.sh <PATH_OF_DOWNLOADED_JAR> <PATH_OF_CONVERTED_JAR>
c) Copy the converted bundles to the {WSO2_SI_Home}/lib directory.
3) Save this sample.

## Executing the Sample:
1) Start the Siddhi application by clicking on 'Run'.
2) If the Siddhi application starts successfully, the following message is shown on the console:
* store-hbase.siddhi - Started Successfully!

## Testing the Sample:
1) Simulate single events as follows:
a) Click on Event Simulator (double arrows on left tab) and then 'Single Simulation'.
b) For the Siddhi App Name, select 'store-hbase'.
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
/* Inserting event into the store */
@info(name='query1')
from insertSweetProductionStream
insert into SweetProductionTable;

/* Deleting event from the store */
/* Updating event from the store */

@info(name = 'query3')
from updateSweetProductionStream
update SweetProductionTable 
on SweetProductionTable.name == name ;

/* Updating or inserting event from the store */
/* Siddhi In in store */
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
@App:name("store-hbase")




@App:description('Receive events via simulator and persist the received data in the store.')

define stream insertSweetProductionStream (name string, amount double);
define stream deleteSweetProductionStream (name string);
define stream searchSweetProductionStream (name string);
define stream updateSweetProductionStream (name string, amount double);
define stream updateOrInsertSweetProductionStream (name string, amount double);
define stream containsSweetProductionStream (name string, amount double);

@sink(type='log')
define stream logStream(name string, amount double);


@Store(type='hbase', hbase.zookeeper.quorum='localhost')
--@primaryKey('name')
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