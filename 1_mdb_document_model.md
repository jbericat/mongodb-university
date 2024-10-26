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

### 2.1. How to build a proper data model?

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

### 2.2. What's thge purpose of having a good data model?

Benfits:

- Make it easier to manage data
- Make queries more efficient
- Use less memory and CPU
- Reduce costs

> General principle at mongoDB -> Data that is accessed together should be stored together

### 2.3. Data modeling is:

- The process of defining how data is stored (Data modeling helps you use your data effectively to meet information needs)
- The process of defining the relationships that exist among different entities in your data

### 2.4. Types of relationship our data can have

- one-to-one (1-1): A data entity in one set is connected to exactly one data entity in another set
- one-to-many (1-*): A data entity in one set is connected to any number of data entities in another set
- many-to-many (\*-*): Any number of entity in one set are connected to any number of data entities in another set

### 2.5. Ways to model relationships

- Embedding: Is when we take related data and store it in the same document
- Referencing: Is when we refer to documents in another collection in our document (using the objectId, which acts as a foreign key)

## 3. Modeling Data Relationships

### 3.1.Embedding

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

### 3.2. Referencing

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

## 4. Embedding Data in Documents

### 4.1. Advantages / PRO's

- Embedding is normally used when we `have one-to-many` or `many-to-many` relationships in the data that's being stored
- avoids application joins
- Enables us to store related data in the same document, which:
  - Improves performance on read operations
  - allows developers to store data in a single write operation

### 4.2. Disadvantages / CON's

Issues that can arise when using embedded data

- Can create **large documents**
- Large documents Can lead to latency in reads
- Excessive memory use (large docs must be load in memory in full) -> leads to slow performance
- Continuously adding data without limit creates unbounded documents (this is, they can grow without limitations)
- **Unbounded documents** may exceed the BSON threshold of 16MB
- Both large documents unbounded documents are **Schema antipatterns** -> https://www.mongodb.com/docs/manual/data-modeling/design-antipatterns/
