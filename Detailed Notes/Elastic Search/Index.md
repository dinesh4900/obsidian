An index in Elasticsearch is a collection of documents that have similar characteristics. It is similar to a database in the relational database world, where a database contains tables, and each table contains rows of data

#### Characteristics

##### Logical Namespace

An index in Elasticsearch acts as a logical namespace that organises related documents. Each index name must be lowercase and can contain letters, digits, and dashes. For example, let's create an index named `employees`:


```
PUT /employees
{
  "settings": {
    "number_of_shards": 3,
    "number_of_replicas": 2
  }
}

```

##### Schema-fee

Elasticsearch is schema-free, meaning you don't need to define a strict schema for your documents upfront. However, you can define mappings to customize how fields are indexed and analysed. Let's index an employee document without explicitly defining a schema:

```
POST /employees/_doc/1
{
  "name": "John Doe",
  "age": 35,
  "position": "Software Engineer",
  "skills": ["Java", "Python", "Elasticsearch"],
  "join_date": "2020-01-15",
  "office_location": "New York"
}

```


##### Distributed

Elasticsearch indexes are distributed by nature, allowing data to be divided into shards for scalability and fault tolerance. Let's expand on our `employees` index to include distributed storage

```
PUT /employees
{
  "settings": {
    "number_of_shards": 5,
    "number_of_replicas": 1
  },
  "mappings": {
    "properties": {
      "name": { "type": "text" },
      "age": { "type": "integer" },
      "position": { "type": "text" },
      "skills": { "type": "keyword" },
      "join_date": { "type": "date", "format": "yyyy-MM-dd" },
      "office_location": { "type": "text" }
    }
  }
}

```

Explanation:
- We update the `employees` index to include mappings that specify how each field should be indexed (`text`, `integer`, `keyword`, `date`).
- Elasticsearch automatically distributes the data across the configured number of shards (`5` primary shards in this example) and manages replication (`1` replica for each primary shard) for fault tolerance and high availability.

### Example
Let's say we are building an e-commerce platform. You might use Elasticsearch to store product data. Here’s how you would conceptualize and use an index in this scenario:

#### Creating an Index:
   First, you create an index named `products` using the Elasticsearch API. This can be done using a simple HTTP PUT request:
   
```
PUT /products
{
  "settings": {
    "number_of_shards": 5,
    "number_of_replicas": 1
  },
  "mappings": {
    "properties": {
      "product_id": { "type": "keyword" },
      "product_name": { "type": "text" },
      "description": { "type": "text" },
      "price": { "type": "float" },
      "in_stock": { "type": "boolean" },
      "categories": { "type": "keyword" }
    }
  }
}
  ```
  
  **settings**: Configures settings such as the number of primary shards (`number_of_shards`) and replicas (`number_of_replicas`). Shards determine the horizontal partitioning of your data.
  
  **mappings**: Defines the schema or structure of your documents within the index. In this example, we specify the types of fields (`product_id`, `product_name`, `description`, `price`, `in_stock`, `categories`) and their data types (`keyword`, `text`, `float`, `boolean`).


#### Indexing Documents:
   Once the index is created, you can start indexing (adding) documents into it. Each document represents a product:
   
```
POST /products/_doc/1
{
  "product_id": "123456",
  "product_name": "Smartphone X",
  "description": "A high-performance smartphone with advanced features.",
  "price": 699.99,
  "in_stock": true,
  "categories": ["Electronics", "Smartphones"]
}
```

POST: this HTTP method is used to add a document in to the products index.

**/products/_doc/1**: `_doc` is the type (deprecated in recent versions of Elasticsearch) and `1` is the unique identifier (ID) of the document. If no ID is specified, Elasticsearch will generate one.

**Document Body**: Contains the actual data (fields and values) of the document.
   
#### Searching Documents:
   You can search for documents within the `products` index using various query parameters:
   
```
GET /products/_search
{
  "query": {
    "match": {
      "product_name": "Smartphone"
    }
  }
}
```

**GET**: This HTTP method is used to retrieve documents from the `products` index.

**/products/_search**: `_search` endpoint is used to perform a search operation within the index.

**Query Body**: Contains the query DSL (Domain-Specific Language) that specifies what you are searching for. In this example, we are searching for products whose name contains the term "Smartphone".