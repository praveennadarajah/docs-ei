*
## Purpose:
This app shows how to publish events to GCS bucket using the siddhi-io-gcs sink extension. An event will be recieved
## by the App by consuming events from the endpoint http://localhost:8006/inputStream and it will be sent to the GCS Sink
o Aggregate and commit an object to a Google Cloud Storage bucket.

## Prerequisites:
1. Download the credential file which is generated through the GCP console and keep it in a folder.

## Executing the Sample:
1. Complete the credential.path and the bucket.name entries in the GCS Sink definition.
2. For the credential.path either you can provide it in the config as shown below or can set a System variable with the name
'GOOGLE_APPLICATION_CREDENTIALS' with the path to the credential file.
3. Run the sample.

## Testing the Sample:
Open a terminal and issue the following curl command
	curl -H "Content-Type: application/json" -d '{"event":{"key":"testFile","payload":"message", "test":"wjson"}}' http://localhost:8006/inputStream

```sql
@App:name("GCSSinkSample")
@App:description("Publish Events to a GCS bucket using a http-source")


@Source(type = 'http',
        receiver.url='http://localhost:8006/inputStream', @map(type='json'))
define stream inputStream(key string, payload string, suffix string);

@sink(type='google-cloud-storage', credential.path='<credential.path>', bucket.name='<bucket.name>',
      object.name='test-object-{{suffix}}',
    @map(type='text'))
define stream outputStream(key string, payload string, suffix string);

from inputStream
select *
insert into outputStream;
```