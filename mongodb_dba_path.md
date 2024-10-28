# MongoDB Database Admin Path (Self-Managed)

This learning path contains a series of units to help you, as a Database Administrator, learn MongoDB knowledge and skills. In this path, you’ll learn MongoDB basics as well as how to administer and maintain a MongoDB database.

## 1. MongoDB and the Document Model

How to manage MongoDB databases, collections, and documents.

### 1.1. MongoDB

General purpose database

### 1.2. Document model

Documents -> Collections -> Containers

### 1.3. MongoDB Basics

- Data is displayed in json and stored in bson
- bson supports not only the json basics datatypes (string, numeric, object, array, nulls, boolean), but also a broader range of datatypes
- objectId is a datatype that can be used inm mdb to identify required files
- Every document requires an _id field
- Collections have flexible schemas that supports polymorphic data (documents in the same collection are not required to share a common structure of fields and value types by default)
- We can set constraints on the structure with optional schema rules

### 1.4. Atlas basics

Atlas = Developer data platform
Atlas Data explorer to manage atlas data -> Provides built-in tools to crate and view databases, collections and documents in an atlas db deployment

## 2. MongoDB Data Modeling Intro

### 2.1. Introduction to Data Modeling

#### 2.1.1. How to build a proper data model?

Ask yourself these questions

- What does my application do?
- What data will I store?
- How will users access this data?
- What data will be the most valuable to me?

this will help us to:

- Describe your task
- Describe what our data looks like
- The relationship among the data
- The tooling you plan to have
- The access patterns that might emerge

This is how we build a proper datamodel

#### 2.1.2. What's the purpose of having a good data model?

Benfits:

- Make it easier to manage data
- Make queries more efficient
- Use less memory and CPU
- Reduce costs

> General principle at mongoDB -> Data that is accessed together should be stored together

#### 2.1.3. Data modeling is:

- The process of defining how data is stored (Data modeling helps you use your data effectively to meet information needs)
- The process of defining the relationships that exist among different entities in your data

### 2.2. Types of Data Relationships

#### 2.2.1. Types of relationship our data can have

- one-to-one (1-1): A data entity in one set is connected to exactly one data entity in another set
- one-to-many (1-*): A data entity in one set is connected to any number of data entities in another set
- many-to-many (\*-*): Any number of entity in one set are connected to any number of data entities in another set

#### 2.2.2. Ways to model relationships

- Embedding: Is when we take related data and store it in the same document
- Referencing: Is when we refer to documents in another collection in our document (using the objectId, which acts as a foreign key)

### 2.3. Modeling Data Relationships

### 2.3.1. Embedding

We can implement one to many using nested dictionaries (which in mdb is called "sub-document embedding" or simply "nested documents"), like:

```json
{
    "_id": 0000000001,
    "name": "Jordi",
    "addresses": [
        {
            "city": "Sabadell",
            "street": "Calvet d'estrella"
        },
        {
            "city": "Cerdanyola",
            "street": "Almogàvers"
        }
    ]
}
```

#### 2.3.2. Referencing

We can use objectId's to reference other documents on the same or different collections within the same DB

```json
{
    "name": "Jordi",
    "address_ids": [
        ObjectId("507f1f77bcf86cd799439011"),
        ObjectId("507f1f77bcf86cd799439012")
    ]
}
```

```json
{
    "_id": ObjectId("507f1f77bcf86cd799439011"),
    "city": "Sabadell",
    "street": "Calvet d'estrella"
}
```

```json
{
    "_id": ObjectId("507f1f77bcf86cd799439012"),
    "city": "Cerdanyola",
    "street": "Almogàvers"
}
```

### 2.4. Embedding Data in Documents

Embedding is normally used when we have `one-to-many` or `many-to-many` relationships in the data that's being stored

#### 2.4.1. Advantages / PRO's

- Simplify queries
- avoids application joins
- Enables us to store related data in the same document, which:
  - Improves performance on read operations
  - allows developers to store data in a single write operation

#### 2.4.2. Disadvantages / CON's

Issues that can arise when using embedded data

- Can create **large documents**
- Large documents Can lead to latency in reads
- Excessive memory use (large docs must be load in memory in full) -> leads to slow performance
- Continuously adding data without limit creates unbounded documents (this is, they can grow without limitations)
- **Unbounded documents** may exceed the BSON threshold of 16MB
- Both large documents & unbounded documents are **Schema anti-patterns** -> https://www.mongodb.com/docs/manual/data-modeling/design-antipatterns/

### 2.5. Referencing Data in Documents

- References save the `_id` field of one document in another document as a link between the two
- Referencing data in documents links documents by using the same field
- Are simple and sufficient for most use cuses
- using refercens is often called linking or data normalization

#### 2.5.1. Advantages / PRO's

- No duplication odf data
- Smaller documents

#### 2.5.2. Disadvantages / CON's

- Need to join data from multiple documents

###  2.6. Scaling a Data Model

Next we will review the problems that can arise when we do not use a proper document mode (that is, documents that are not scalable).

MongoDB first principle of **data that are accessed together should be stored together**" is mostly because our query pattern (how we access the data) needs to align with our data model in order to have **optimum efficiency** of query result time, memory usage, CPU usage and storage.

#### 2.6.1. Effective data modeling

An effective data modeling will prevent all those problems. For example, with data embedding we can easily retrieve all the data we want with a single read, but problems will arise as the embedded data grows larger:

- The document will take up more space in memory
- This data model amy impact write performance, as the entire document is rewritten into mongoDB data storage
- It becomes difficult to perform pagination of data

Summing-up, we should consider sacrificing the benefits of embedded data model in order to avoid the falling onto these problems. Instead, we could break our data up into multiple collections and use references to keep frequently accessed data together.

#### 2.6.2. Things we should avoid

- Documents with size more than 16MB (unbounded documents)
- Poor query performance
- Poor write performance
- Too much memory being used

### 2.7 Using Atlas Tools for Schema Help

MongoDB Atlas provides tools to identify schema anti-patterns:

- Data explorer
- Performance advisor

Schema design patterns are guidelines that help developers to;

- plan data
- organize data
- model data

#### 2.7.1. Schema best practices

When applications are developed without following schema best practices, it can result in:

- Suboptimal performance
- Non-scalable solutions

#### 2.7.2. Common schema anti-patterns

- Massive arrays
- Massive number of collections
- Bloated documents
- Unnecessary indexes
- Queries without indexes
- Data that's accessed together, but stored in different collections

It's not always easy to recognize these anti-patterns, but some of the tools available on Mdb atlas cam help to identify and resolve them.

#### 2.7.3. Data explorer

- Is availabe on the free-tier on atlas
- It allows us to see:
  - Storage size
  - Number of documents
  - Total index size
- The indexes tab shows the collection indexes and their stats:
  - It help us identify unnecessary indexes that can be dropped
  - A common troubleshooting practice is to order the indexes by its usage and see the ones that have not been used based on the recent queries, so we can determine which ones can be deleted. This would reduce the storage needed by a collection, which traduces into less storage consumption and memory overhead. This can also be achieved with the `db.collection.stats()` method
- The schema anti-pattern tab highlights any issues in a collection:
  - provides details to resolve them
  - By selecting "how-to fix this issue" we can see some guidelines on how to fix these issues

#### 2.7.4. Performance advisor

- It can tell us which indexes are redundant
- When working in an M10 tier or higher the performance advisor section provides **recommendations to improve the performance** of the most active collections and **collections with slow-running queries**
- Shows index recommendations
- identifies unnecessary indexes
- highlight issues with the schema

## 3. The Mongodb Shell

### 3.1. Installing and Connecting to the MongoDB Shell

First we'll learn how to install the `mongosh` in an ubuntu OS and then we'll connect to an Atlas cluster.

Is a powerfull shell for mongoDB. It gives users a friendly interface for managing, interacting with and testing queries against your database.



#### 3.1.1. Installing mongosh

```bash
$ sudo apt update
sudo apt install gnupg

```

#### 3.1.2. Connecting to an Atlas cluster


Connection to an atlas cluster requiers the Atlas connection string

Atlas connection string allows us to include authentication strings, which makes authentication using

```bash


```

#### 3.1.3. Common modifiers, helpers and methods for the mongosh

`modifiers` are parameters we can include when running the mongosh command:

```bash
--port 27017
--apiversion
--username
```

`helpers` are used to interact with databases and collections:

```bash
db # Return the current db we're connected to
use db_name # Switch to a different db
show collections # To view all collections within a container / db
show dbs # to list all dbs on the server
```

`CRUD` operations allows us to manipulate our data:

db.collection_name.insertOne({...})
db.collection_name.updateOne({...})
db.collection_name.deleteOne({...})

`methods` are used to run various queries on our collections:

```bash
db.collection.find() # Finds all documents in a collection
db.collection.findOne({...}) # Finds an specific document in a collection
```
