# Elasticsearch-Guide
Complete Guide to Elasticsearch

Source: [udemy](https://www.udemy.com/course/elasticsearch-complete-guide/)

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

