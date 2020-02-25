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
