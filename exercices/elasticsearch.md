# Elasticsearch
test
On this part, we would dive into the logs we ingested on the logstash part.

### useful curl commands

We need to ensure that elasticsearch is running, the health of our cluster, that the index has been created on elasticsearch and that our data are correctly saved on that topic.


```
http://localhost:9200/_cat/health?v
http://localhost:9200/_cat/indices?v
http://localhost:9200/Your-index/_search?pretty&q=response=200
```
(Don't forget to change your index with your index)

### Index and documents

Before digging more into our logstash data, let's manually create the main elasticsearch objects

For windows users, install cygwin so we can make curl commands to the elasticsearch API. https://cygwin.com/install.html

- Let's create an index.
```
curl -XPUT -u elastic:changeme 'localhost:9200/accounts?pretty'
curl -XGET -u elastic:changeme 'localhost:9200/_cat/indices?v'
```

Result: 1 index named 'customer' with 5 primary shards and 1 replica (the defaults) and it contains 0 documents in it.
We can delete index and recreate it with different settings

```
curl -XDELETE -u elastic:changeme 'localhost:9200/accounts'
curl -XPUT -u elastic:changeme 'localhost:9200/accounts' -H 'Content-Type: application/json' -d'{   "settings": {     "number_of_replicas": 1,     "number_of_shards": 7   } }'
```

- Add documents to this index

```
curl -XPUT -u elastic:changeme -H 'Content-Type: application/json' 'localhost:9200/accounts/person/1?pretty' -d '
{
    "name" : "John",
    "lastname" : "Doe",
    "job_description" : "Systems administrator and Linux specialit"
}'
```

1 and 2 are the ids of the document. Remember Ids are unique per index.

- Query that document
```
curl -XGET -u elastic:changeme 'localhost:9200/accounts/person/1?pretty'
```

The result will contain metadata and also the full document (shown in the _source field) :

The keen reader already realized that we made a typo in the job description (specialit); let’s correct it by updating the document (_update):

- Update that document

```
curl -XPOST -u elastic:changeme -H 'Content-Type: application/json' 'localhost:9200/accounts/person/1/_update?pretty' -d '
{
      "doc":{
          "job_description" : "Systems administrator and Linux specialist"
       }
}'
```

Let's now add a second document :

```
curl -XPUT -u elastic:changeme -H 'Content-Type: application/json' 'localhost:9200/accounts/person/2?pretty' -d '
{
    "name" : "John",
    "lastname" : "Smith",
    "job_description" : "Systems administrator"
}'
```

So far, we did retrieve documents by id, but we did not do searches. When querying using the Rest API we can pass the query in the body of the request or directly in the URL with a specific syntax. In this section we will do searches directly in the URL in the format /_search?q=something:


- Search in elasticsearch
```
curl -XGET -u elastic:changeme 'localhost:9200/_search?q=John'
```

This search will return both documents, since both of them include john:

In this result we can see the matching documents and also some metadata like the total number of results for the query. Let´s keep doing more searches. Before running the searches, try to figure out by yourself what documents will be retrieved (the response comes after the command):

```
curl -XGET -u elastic:changeme 'localhost:9200/_search?q=smith'
```

This search will return only the last document we added, the only one containing smith.

```
curl -XGET -u elastic:changeme 'localhost:9200/_search?q=job_description:john'
```

This search will not return any document. In this case, we are restricting the search only to the field job_description, which does not contain the term. As an exercise for the reader, try to do:

a search in that field that will return only the document with id 1
a search in that field returning both documents
a search in that field returning only the document with id 2 - you will need a hint: the “q” parameter uses the same syntax as the Query String.

```
curl -XGET -u elastic:changeme 'localhost:9200/_search?q=lastname:Smith'
```
```
curl -XGET -u elastic:changeme 'localhost:9200/_search?q=name:john'
```
```
curl -XGET -u elastic:changeme 'localhost:9200/_search?q=lastname:(Doe OR Smith)'
```

is it possible to search only within a specific index? The answer is yes: we can specify the index and type in the URL. Try this:


```
curl -XGET -u elastic:changeme 'localhost:9200/accounts/person/_search?q=job_description:linux'
```

Additionally to searching in one index, we can search in multiple indices at the same time by providing a comma-separated list of indices names, and the same can be done for types. There are more options: information about them can be found in Multi-Index, Multi-type. As an exercise for the reader, add documents to a second (different) index and do searches in both indices simultaneously.

To close this section, we will delete a document, and then the entire index. After deleting the document, try to retrieve or find it in searches.

```
curl -XDELETE -u elastic:changeme 'localhost:9200/accounts/person/1'
```

Finally, we can delete the full index.

```
curl -XDELETE -u elastic:changeme 'localhost:9200/accounts'
```


And this is the end of the first section. Let’s summarize what we did:

Added a document. Implicitly, an index was created (the index did not previously exist).
Retrieved the document.
Updated the document, to correct a typo, and check it was corrected.
Added a second document.
Searched, including searches using implicitly all of the fields, and a search focused only in one field.
Proposed several search exercises.
Explained the basics on searching in several indices and types simultaneously.
Proposed searching in several indices simultaneously.
Deleted one document.
Deleted an entire index.


https://www.elastic.co/blog/a-practical-introduction-to-elasticsearch



### search queries and aggregations

In order to execute more complex queries and aggregations, we will need to insert more data. For doing so, we will use the elasticsearch Bulk API (https://www.elastic.co/guide/en/elasticsearch/reference/5.5/docs-bulk.html).

We first create the mapping of our index based on the data structure.

Shakespeare data structure:
```
{
    "line_id": INT,
    "play_name": "String",
    "speech_number": INT,
    "line_number": "String",
    "speaker": "String",
    "text_entry": "String",
}
```

Mapping in Elasticsearch:
```
PUT /shakespeare
{
  "mappings": {
    "properties": {
    "speaker": {"type": "keyword"},
    "play_name": {"type": "keyword"},
    "line_id": {"type": "integer"},
    "speech_number": {"type": "integer"}
    }
  }
}
```

cd elasticsearch/data

```
curl -u elastic:changeme -H 'Content-Type: application/x-ndjson' -XPOST 'localhost:9200/bank/account/_bulk?pretty' --data-binary @accounts.json
curl -u elastic:changeme -H 'Content-Type: application/x-ndjson' -XPOST 'localhost:9200/shakespeare/_bulk?pretty' --data-binary @shakespeare.json
curl -u elastic:changeme -H 'Content-Type: application/x-ndjson' -XPOST 'localhost:9200/_bulk?pretty' --data-binary @logs.jsonl
```

or for windows users in powershell

```
Invoke-RestMethod "http://localhost:9200/bank/account/_bulk?pretty" -Method Post -ContentType 'application/x-ndjson' -InFile "accounts.json"
Invoke-RestMethod "http://localhost:9200/shakespeare/doc/_bulk?pretty" -Method Post -ContentType 'application/x-ndjson' -InFile "shakespeare_6.0.json"
Invoke-RestMethod "http://localhost:9200/_bulk?pretty" -Method Post -ContentType 'application/x-ndjson' -InFile "logs.jsonl"
```

Verify succesfull loading

```
GET /_cat/indices?v
```
