# Elasticsearch-Guide
Complete Guide to Elasticsearch

Source: [udemy](https://www.udemy.com/course/elasticsearch-complete-guide/)

## Table of Contents
- [Setup](#setup)
  - [Elasticsearch](#elasticsearch)
  - [Kibana](#kibana)
- [Test Cluster and Nodes](#test-cluster-and-nodes)
- [Sending Queries with cURL](#sending-queries-with-curl)
- [Creating an Index, Verify Its Current State and Deleting It](#creating-an-index-verify-its-current-state-and-deleting-it)
- [Creating and Configuring an Index](#creating-and-configuring-an-index)
- [Indexing Documents](#indexing-documents)
- [Retrieving Documents by ID](#retrieving-documents-by-id)
- [Updating Documents](#updating-documents)
- [Scripted Updates](#scripted-updates)
- [Upserts](#upserts)
- [Replacing Documents](#replacing-documents)
- [Deleting Documents](#deleting-documents)
- [Optimistic Concurrency Control](#optimistic-concurrency-control)
- [Update by Query](#update-by-query)
- [Delete by Query](#delete-by-query)
- [Batch Processing](#batch-processing)
- [Importing Data with cURL](#importing-data-with-curl)
- [Dynamic Mapping](#dynamic-mapping)
  - [Meta Fields](#meta-fields)
  - [Adding Mappings to Existing Indices](#adding-mappings-to-existing-indices)
  - [Changing Existing Mappings](#changing-existing-mappings)
  - [Mapping Parameters](#mapping-parameters)



## Setup

### Elasticsearch

Download: [ElasticSearch](https://www.elastic.co/downloads/elasticsearch)

**Download and extract to the Documents directory**

```shell
cd Documents/elasticsearch-7.6.0

bin\elasticsearch.bat
```

**Default Port**

```shell
localhost:9200
```

### Kibana

Download: [Kibana](https://www.elastic.co/downloads/kibana)

**Download and extract to the Documents directory**

```shell
cd Documents/kibana-7.6.0-windows-x86_64

bin\kibana.bat
```

**Default Port**

```shell
localhost:5601
```

## Test Cluster and Nodes

Go to localhost:5601 -> Navigate to Dev Tools -> Test the following requests:

Documentation: [cluster-nodes](https://www.elastic.co/guide/en/elasticsearch/reference/current/cluster-nodes-info.html)

```shell
GET /_cluster/health
```

Documentation: [cat-nodes](https://www.elastic.co/guide/en/elasticsearch/reference/current/cat-nodes.html)

```shell
GET /_cat/nodes?v
```

```shell
GET /_cat/indices?v
```

## Sending Queries with cURL
In front of each line there is a Wrench Icon -> Click "Copy as cURL" -> Open Command Prompt and paste the cURL

**NOTE: if the cURL has single quotes change it to double quotes, then escape each inner double quote**

```shell
curl -XGET "http://localhost:9200/.kibana/_search" -H "Content-Type: application/json" -d"{  \"query\": {   \"match_all\": {}  }}"
```

## Creating an Index, Verify Its Current State and Deleting It

```shell
PUT /pages

GET /_cluster/health

GET /_cat/indices?v

GET /_cat/shards?v

DELETE /pages
```

## Creating and Configuring an Index

```shell
PUT /products
{
  "settings": {
    "number_of_shards": 2,
    "number_of_replicas": 2
  }
}
```

## Indexing Documents

```shell
POST /products/_doc
{
  "name": "Coffee Maker",
  "price": 64,
  "in_stock": 10
}
```

**NOTE: If document doesn't exist, it will create a new one**

```shell
PUT /products/_doc/100
{
  "name": "Toaster",
  "price": 49,
  "in_stock": 4
}
```

## Retrieving Documents by ID

```shell
GET /products/_doc/100
```

## Updating Documents

**NOTE: Documents aren't changed, instead they are replaced!**

```shell
POST /products/_update/100
{
  "doc": {
    "in_stock": 3,
    "tags": ["electronics"]
  }
}

GET products/_doc/100
```

## Scripted Updates

```shell
# Decrements the value on 'in_stock'
POST /products/_update/100
{
  "script": {
    "source": "ctx._source.in_stock--"
  }
}

# Sets the value of 'in_stock' to the '10'
POST /products/_update/100
{
  "script": {
    "source": "ctx._source.in_stock = 10"
  }
}

# Sets 'params.quantity' to '4' and subtracts that value to the 'in_stock'
POST /products/_update/100
{
  "script": {
    "source": "ctx._source.in_stock -= params.quantity",
    "params": {
      "quantity": 4
    }
  }
}

# Verifies condition - if 'in_stock' has the value of '0' inserts the value 'noop' on 'op', else decrements the value on 'in_stock'
POST /products/_update/100
{
  "script": {
    "source": """
      if(ctx._source.in_stock > 0) {
        ctx._source.in_stock--;
      }
    """
  }
}

GET products/_doc/100
```

## Upserts

```shell
# Creates a document if none exists 'upsert', if a document already exists performs the 'script'
POST /products/_update/101
{
  "script": {
    "source": "ctx._source.in_stock++"
  },
  "upsert": {
    "name": "Blender",
    "price": 399,
    "in_stock": 5
  }
}

GET products/_doc/101
```

## Replacing Documents

```shell
PUT /products/_doc/100
{
    "name": "Toaster",
    "price": 79,
    "in_stock": 4
}

GET products/_doc/100
```

## Deleting Documents

```shell
DELETE /products/_doc/101

GET products/_doc/101
```

## Optimistic Concurrency Control

```shell
GET products/_doc/100

# Controls the update based on 'primary_term' and 'seq_no'
POST products/_update/100?if_primary_term=2&if_seq_no=14
{
  "doc": {
    "in_stock": 123
  }
}

GET products/_doc/100
```

## Update by Query

```shell
# Updates all products based on the query
POST products/_update_by_query
{
  "script": {
    "source": "ctx._source.in_stock--"
  },
  "query": {
    "match_all": {}
  }
}

GET products/_doc/100
```

```shell
# Updates all products based on the query and proceeds even with conflicts
POST products/_update_by_query
{
  "conflicts": "proceed",
  "script": {
    "source": "ctx._source.in_stock--"
  },
  "query": {
    "match_all": {}
  }
}
```

## Delete by Query

```shell
# Deletes all products
POST products/_delete_by_query
{
  "query": {
    "match_all": {}
  }
}
```

## Batch Processing

Documentation: [docs-bulk](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-bulk.html)

```shell
# "index" creates document or replaces if already exists
# "create" creates document or does nothing if already exists
POST /_bulk
{ "index": { "_index": "products", "_id": 200 } }
{ "name": "Espresso Machine", "price": 199, "in_stock": 5 }
{ "create": { "_index": "products", "_id": 201 } }
{ "name": "Milk Frother", "price": 149, "in_stock": 14 }

GET /products/_search
{
  "query": {
    "match_all": {}
  }
}
```

```shell
# "update" and "delete" documents
POST /_bulk
{ "update": { "_index": "products", "_id": 201 } }
{ "doc": { "price": 129 } }
{ "delete": { "_index": "products", "_id": 200 } }

GET /products/_search
{
  "query": {
    "match_all": {}
  }
}
```
OR
```shell
# "update" and "delete" documents
POST /products/_bulk
{ "update": { "_id": 201 } }
{ "doc": { "price": 129 } }
{ "delete": { "_id": 200 } }

GET /products/_search
{
  "query": {
    "match_all": {}
  }
}
```

## Importing Data with cURL

Download: [cURL](https://curl.haxx.se/download.html)

On the command prompt type:

```shell
cd /Elasticsearch-Guide

curl -H "Content-Type: application/x-ndjson" -XPOST http://localhost:9200/products/_bulk --data-binary "@products-bulk.json"
```

On the Kibana Dev Tools check how many products were created: 

**NOTE: It might take a couple of minutes before showing all documents assigned**

```shell
GET /_cat/shards?v
```

## Dynamic Mapping

Documentation: [dynamic-mapping](https://www.elastic.co/guide/en/elasticsearch/reference/current/dynamic-field-mapping.html)

```shell
GET /products/_mapping
```

## Meta Fields

| Meta Fields | Description |
| --- | --- |
| `_index` | Contains the name of the index to which a document belongs. |
| `_id` | Stores the ID of documents. |
| `_source` | Contains the original JSON object used when indexing a document. |
| `_field_names` | Contains the name of every field that contains a non-null value. |
| `_routing` | Stores the value used to route a document to a shard. |
| `_version` | Stores the internal version of a document. |
| `_meta` | May be used to store custom data that is left untouched by Elasticsearch (store application specific data). |

## Adding Mappings to Existing Indices

```shell
PUT /products/_mapping
{
  "properties": {
    "discount": {
      "type": "double"
    }
  }
}

GET /products/_mapping
```

## Changing Existing Mappings

```shell
PUT /product
{
  "mappings": {
    "dynamic": false,
    "properties": {
      "in_stock": {
        "type": "integer"
      },
      "is_active": {
        "type": "boolean"
      },
      "price": {
        "type": "integer"
      },
      "sold": {
        "type": "long"
      }
    }
  }
}`
```

## Mapping Parameters

| Mapping Parameters | Description |
| --- | --- |
| `coerce` | Converts values to the proper data type. |
| `copy_to` | Creates custom fields. |
| `dynamic` | Enables/disables adding fields to documents or inner objects dynamically. |
| `properties` | Use to wrap field mappings. |
| `norms` | Enables/disables the storaging of norms for relevance scores. |
| `format` | Defines the format for date fields. |
| `null_value` | Replaces NULL values with the specified value. |
| `fields` | Used to index fields in different ways. |