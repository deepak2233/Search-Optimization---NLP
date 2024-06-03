# Exploring Elasticsearch Cluster with Kibana

## Introduction

In this post, we will explore an Elasticsearch cluster using Kibana's Console tool. We'll learn how to send requests to Elasticsearch and interpret the responses. This is a beginner-friendly guide, so don't worry if you're new to these tools.

## What is a REST API?

A REST API (Representational State Transfer Application Programming Interface) is a way to interact with web services using HTTP requests. It follows a set of constraints that make it easy to build scalable and efficient web services. The main HTTP verbs used in REST APIs are:

- **GET**: Retrieve data from the server.
- **POST**: Send data to the server to create a new resource.
- **PUT**: Update an existing resource on the server.
- **DELETE**: Remove a resource from the server.

## How Elasticsearch Works with REST API

Elasticsearch is also uses a REST API to interact with clients. This means you can use standard HTTP methods to perform operations on your Elasticsearch cluster. Each operation you want to perform, such as searching for documents, indexing new data, or retrieving cluster health, is done through a specific API endpoint.


## Getting Started with Kibana Console

First, let's open the Console tool in Kibana. You can find it by expanding the menu and clicking on "Dev Tools" in the "Management" section.

The Console tool allows us to send requests to Elasticsearch's REST API. This means we can use HTTP verbs like GET, POST, PUT, and DELETE to interact with our Elasticsearch cluster.

## Sending Our First Request

### Checking Cluster Health

Let's start by checking the health of our Elasticsearch cluster. Since we want to retrieve information, we'll use the GET verb.

In the Console tool, type the following:

```http
GET /_cluster/health
```

This request will return a JSON object with details about the cluster's health. Here's what each part means:

- **GET:** The HTTP verb to retrieve data.
- **/_cluster/health:** The path to access the cluster health information.

When you run this query, you should see a response like this:

```
{
  "cluster_name": "elasticsearch",
  "status": "green",
  "timed_out": false,
  "number_of_nodes": 1,
  "number_of_data_nodes": 1,
  ...
}
```

The status field shows the health of the cluster. A "green" status means everything is fine.

### Listing Cluster Nodes
Next, let's list the nodes in our cluster. For this, we'll use the CAT API, which provides data in a human-readable format.

Type the following query in the Console tool:
```
GET /_cat/nodes?v
```

The v query parameter adds headers to the output, making it easier to read.

The response will look something like this:

```
ip        heap.percent ram.percent cpu load_1m load_5m load_15m node.role master name
127.0.0.1           8          94   2    0.00    0.02     0.05 mdi       *      node-1
```

This shows basic information about each node, such as its IP address, memory usage, CPU load, and roles.

## Understanding Indices
Indices in Elasticsearch are like tables in a relational database. They store data in a structured format.

### Checking Indices
To see which indices are in our cluster, use the following query:
```
GET /_cat/indices?v
```

If no indices are returned, it means we haven't added any yet. But there are system indices used by Elasticsearch and Kibana for storing configurations and other data.

To see these hidden system indices, add the expand_wildcards query parameter:

```
GET /_cat/indices?expand_wildcards=all&v
```

- **What is _cat?**
    - The **_cat** APIs in Elasticsearch are designed to provide information in a format that is easy for humans to read. They are concise and can be used to quickly get an overview of various aspects of the cluster, such as nodes, indices, shards, and more. The output is often displayed in a tabular format, making it easy to scan and understand.

- **What is ?v?**
    - The **?v** query parameter stands for "verbose". When added to a **_cat** API request, it includes header names in the output. This makes it easier to understand the columns in the response.


- **Without ?v, you might get an output like this:**

```
green  open   .kibana_task_manager_1    NJkLdPFeRoOdL2HeNoJ7fA   1   0          2            0     12.3kb         12.3kb
green  open   .kibana_1                 7WV9dRA5QH6LByJ0quLg5A   1   0          4            0     34.5kb         34.5kb

```

- **With ?v, the output includes headers:**

```
health status index                     uuid                   pri rep docs.count docs.deleted store.size pri.store.size
green  open   .kibana_task_manager_1    NJkLdPFeRoOdL2HeNoJ7fA   1   0          2            0     12.3kb         12.3kb
green  open   .kibana_1                 7WV9dRA5QH6LByJ0quLg5A   1   0          4            0     34.5kb         34.5kb
```


## Elasticsearch operations using PUT, POST, and GET requests:

#### PUT Operation (Indexing a Document)
```
PUT /my-index/_doc/1
{
  "name": "John Doe",
  "age": 30
}
```

- This operation indexes a document with ID 1 into the "my-index" index.

#### POST Operation (Searching Documents)

```
POST /my-index/_search
{
  "query": {
    "match": {
      "name": "John"
    }
  }
}
```
- This operation searches for documents in the "my-index" index where the "name" field matches "John"


#### GET Operation (Retrieving a Document)
```
GET /my-index/_doc/1
```
- This operation retrieves the document with ID 1 from the "my-index" index.

#### DELETE Operation (Deleting an Index)

```
DELETE /my-index
```

- This operation deletes the "my-index" index and all its documents.



## Real-Life Example: Monitoring a Website
Imagine you run a website and use Elasticsearch to store logs and monitor performance. By checking the cluster health and node status, you can ensure everything is running smoothly. If the cluster status turns yellow or red, you can investigate and fix issues before they affect your users.

For example, you could set up an alert to notify you if the cluster health is not green:

```
GET /_cluster/health
```

And you could regularly check the load on your nodes:
```
GET /_cat/nodes?v
```

his helps you keep your website fast and reliable.

## Summary
In this post, we learned how to use Kibana's Console tool to interact with an Elasticsearch cluster. We checked the cluster health, listed nodes, and viewed indices. These basic operations are crucial for managing and monitoring an Elasticsearch cluster effectively.

Stay tuned for more detailed explorations in upcoming posts!