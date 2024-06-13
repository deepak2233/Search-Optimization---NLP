# Introduction

Elasticsearch is a powerful distributed search and analytics engine, widely used for real-time indexing, search, and analysis of data. In this guide, we'll delve into various Elasticsearch operations, covering everything from creating and deleting indices to advanced topics like batch processing and optimistic concurrency control. Along the way, we'll provide detailed coding examples to help you grasp each concept effectively.

---

## Creating & Deleting Indices

Creating an index in Elasticsearch is the first step towards storing your data. Indices are logical namespaces that map to physical data storage. Here's how you can create and delete indices using Elasticsearch's RESTful API:

```python
# Creating an index
PUT /shopping
{
  "settings": {
    "number_of_shards": 1,
    "number_of_replicas": 0
  }
}

# Output
{
  "acknowledged": true,
  "shards_acknowledged": true,
  "index": "shopping"
}


# Deleting an index
DELETE /shopping

# Output

{
  "acknowledged": true
}

```

## Indexing Documents

Indexing documents involves adding structured JSON data to an index. Let's create an example document representing shopping details and index it:

```python
# Indexing a document
PUT /shopping/_doc/1
{
  "product": "Laptop",
  "price": 1000,
  "quantity": 5,
  "category": "Electronics"
}

# Output
{
  "_index": "shopping",
  "_type": "_doc",
  "_id": "1",
  "_version": 1,
  "result": "created",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  },
  "_seq_no": 0,
  "_primary_term": 1
}

```

## Retrieving Documents by ID

Retrieving documents by their unique identifier (ID) is a common operation in Elasticsearch:
```python
# Retrieving a document by ID
GET /shopping/_doc/1

# Output
{
  "_index": "shopping",
  "_type": "_doc",
  "_id": "1",
  "_version": 1,
  "_seq_no": 0,
  "_primary_term": 1,
  "found": true,
  "_source": {
    "product": "Laptop",
    "price": 1000,
    "quantity": 5,
    "category": "Electronics"
  }
}

```

## Updating Documents

You can update existing documents in Elasticsearch:

```python
# Updating a document
POST /shopping/_update/1
{
  "doc": {
    "price": 1200
  }
}

# Output

{
  "_index": "shopping",
  "_type": "_doc",
  "_id": "1",
  "_version": 2,
  "result": "updated",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  }
}

```

## Scripted Updates

Elasticsearch allows you to perform scripted updates using Painless scripting language:

```python
# Scripted update
POST /shopping/_update/1
{
  "script": {
    "source": "ctx._source.price += params.increment",
    "params": {
      "increment": 200
    }
  }
}

# Output
{
  "_index": "shopping",
  "_type": "_doc",
  "_id": "1",
  "_version": 3,
  "result": "updated",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  }
}

```

## Upserts

Upserts are a combination of "update" and "insert" operations. If a document exists, it will be updated; otherwise, a new document will be created:

```python
# Upsert operation
POST /shopping/_update/2
{
  "doc": {
    "product": "Smartphone",
    "price": 800,
    "quantity": 10,
    "category": "Electronics"
  },
  "doc_as_upsert": true
}

# Output:

{
  "_index": "shopping",
  "_type": "_doc",
  "_id": "2",
  "_version": 1,
  "result": "created",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  }
}

```

## Replacing Documents

Replacing documents involves completely replacing an existing document with a new one:

```python
# Replace operation
PUT /shopping/_doc/1
{
  "product": "Tablet",
  "price": 500,
  "quantity": 3,
  "category": "Electronics"
}

# Output:

{
  "_index": "shopping",
  "_type": "_doc",
  "_id": "1",
  "_version": 4,
  "result": "updated",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  }
}

```

## Deleting Documents

Deleting documents removes them from the index:
```python
# Deleting a document
DELETE /shopping/_doc/1

# Output:
{
  "_index": "shopping",
  "_type": "_doc",
  "_id": "1",
  "_version": 5,
  "result": "deleted",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  }
}

```

## Understanding Routing

Routing determines which shard a document will be stored in:

```python
# Indexing with routing
PUT /shopping/_doc/1?routing=user123
{
  "product": "Headphones",
  "price": 50,
  "quantity": 20,
  "category": "Electronics"
}

# Output:

```json
{
  "_index": "shopping",
  "_type": "_doc",
  "_id": "1",
  "_version": 1,
  "result": "created",
  "_shards": {
    "total": 2,
    "successful": 1,
    "failed": 0
  },
  "_seq_no": 0,
  "_primary_term": 1
}
```


### How Elasticsearch Reads Data

* Elasticsearch reads data efficiently by utilizing inverted indexes and distributed search capabilities.

### How Elasticsearch Writes Data

* Elasticsearch writes data by indexing documents into shards and replicas, ensuring fault tolerance and scalability.

### Understanding Document Versioning

* Document versioning in Elasticsearch allows you to track changes to documents over time.

### Optimistic Concurrency Control

* Optimistic concurrency control ensures data consistency by checking document versions before updates.

## Update by Query

* Update by query allows you to perform bulk updates based on a query criteria:

```python
# Update by query
POST /shopping/_update_by_query
{
  "script": {
    "source": "ctx._source.price += params.increment",
    "params": {
      "increment": 50
    }
  },
  "query": {
    "match": {
      "category": "Electronics"
    }
  }
}

# Output:

{
  "took": 16,
  "timed_out": false,
  "total": 1,
  "updated": 1,
  "deleted": 0,
  "batches": 1,
  "version_conflicts": 0,
  "noops": 0,
  "retries": {
    "bulk": 0,
    "search": 0
  },
  "throttled_millis": 0,
  "requests_per_second": -1,
  "throttled_until_millis": 0,
  "failures": []
}

```

## Delete by Query

Delete by query allows you to delete documents based on a query criteria:

```python
# Delete by query
POST /shopping/_delete_by_query
{
  "query": {
    "range": {
      "price": {
        "lte": 100
      }
    }
  }
}

# Output:
{
  "took": 19,
  "timed_out": false,
  "total": 1,
  "deleted": 1,
  "batches": 1,
  "version_conflicts": 0,
  "noops": 0,
  "retries": {
    "bulk": 0,
    "search": 0
  },
  "throttled_millis": 0,
  "requests_per_second": -1,
  "throttled_until_millis": 0,
  "failures": []
}

```
## Batch Processing

Batch processing involves performing operations on multiple documents in a single request:
    
    
```python
# Bulk indexing
POST /shopping/_bulk
{"index": {"_id": "1"}}
{"product": "Keyboard", "price": 30, "quantity": 15, "category": "Electronics"}
{"index": {"_id": "2"}}
{"product": "Mouse", "price": 20, "quantity": 25, "category": "Electronics"}

# Output:

{
  "took": 16,
  "errors": false,
  "items": [
    {
      "index": {
        "_index": "shopping",
        "_type": "_doc",
        "_id": "1",
        "_version": 1,
        "result": "created",
        "_shards": {
          "total": 2,
          "successful": 1,
          "failed": 0
        },
        "_seq_no": 9,
        "_primary_term": 1
      }
    },
    {
      "index": {
        "_index": "shopping",
        "_type": "_doc",
        "_id": "2",
        "_version": 1,
        "result": "created",
        "_shards": {
          "total": 2,
          "successful": 1,
          "failed": 0
        },
        "_seq_no": 10,
        "_primary_term": 1
      }
    }
  ]
}

```

## Importing Data with cURL

You can import data into Elasticsearch using cURL commands:

```python
# Importing data with cURL
curl -XPOST 'localhost:9200/shopping/_bulk' -H 'Content-Type: application/json' --data-binary @data.json

# Ouptut:

{
  "took": 16,
  "errors": false,
  "items": [
    {
      "index": {
        "_index": "shopping",
        "_type": "_doc",
        "_id": "1",
        "_version": 1,
        "result": "created",
        "_shards": {
          "total": 2,
          "successful": 1,
          "failed": 0
        },
        "_seq_no": 9,
        "_primary_term": 1
      }
    },
    {
      "index": {
        "_index": "shopping",
        "_type": "_doc",
        "_id": "2",
        "_version": 1,
        "result": "created",
        "_shards": {
          "total": 2,
          "successful": 1,
          "failed": 0
        },
        "_seq_no": 10,
        "_primary_term": 1
      }
    }
  ]
}

```

