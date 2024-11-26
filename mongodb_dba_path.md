# MongoDB Database Admin Path (Self-Managed)

This learning path contains a series of units to help you, as a Database Administrator, learn MongoDB knowledge and skills. In this path, you’ll learn MongoDB basics as well as how to administer and maintain a MongoDB database.

> This information was obtained from MongoDB University https://learn.mongodb.com/.........

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

> This information was obtained from https://learn.mongodb.com/learn/course/the-mongodb-shell

### 3.1. Installing and Connecting to the MongoDB Shell

First we'll learn how to install the `mongosh` in an ubuntu OS and then we'll connect to an Atlas cluster.

Is a powerfull shell for mongoDB. It gives users a friendly interface for managing, interacting with and testing queries against your database.



#### 3.1.1. Installing mongosh

**Ubuntu 22.04**

```bash
sudo su
apt update
apt install gnupg
wget -qO - https://www.mongodb.org/static/pgp/server-6.0.asc | sudo apt-key add -
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntufocal/mongodb-org/6.0 multiverse" | tee /etc/apt/sources.list.d/mongodb-org-6.0.list
apt update
apt install -y mongodb-mongosh
mongosh --version
```

**ubuntu 24.04**

```bash
sudo su
curl -fsSL https://www.mongodb.org/static/pgp/server-6.0.asc | gpg -o /usr/share/keyrings/mongodb-server-6.0.gpg --dearmor
echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-6.0.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/6.0 multiverse" | tee /etc/apt/sources.list.d/mongodb-org-6.0.list
sudo apt update
sudo apt install -y mongodb-mongosh
```

#### 3.1.2. Connecting to an Atlas cluster

Connection to an atlas cluster requiers the Atlas connection string

Atlas connection string allows us to include authentication strings, which makes authentication using

```bash
mongosh "mongodb+srv://<username>:<password>@<cluster_name>.example.mongodb.net"
```

Here’s what each part of the connection string does:

- mongodb+srv://: Specifies that you are connecting to a MongoDB Atlas cluster using the DNS seed list connection format.
- myAtlasDBUser:myatlas-001: The username and password for your MongoDB database.
- myatlasclusteredu.7so1q.mongodb.net: The hostname of your MongoDB Atlas cluster.
- sample_analytics: The database you’re connecting to.
- ?appName=config-mongosh: Specifies that the application name is "config-mongosh."

**Use of appName**

Setting appName can be helpful in these cases:

- **Diagnostics:** When you monitor or diagnose your MongoDB instance, the appName appears in the server logs, which makes it easier to identify which application or connection is responsible for specific actions.
- **Connection Pooling:** If you have multiple applications connecting to the same database, using distinct appName values allows you to track resource usage and connection pooling for each application independently.
- **Logging and Metrics:** MongoDB Atlas, for example, can display metrics per appName, helping you analyze the behavior of different clients.

#### 3.1.3. Common modifiers, helpers and methods for the mongosh

`modifiers` are parameters we can include when running the mongosh command:

```bash
--port 27017
--apiversion
--username
```

`helpers` are used to interact with databases and collections:

```sh
db # Return the current db we're connected to
use db_name # Switch to a different db
show collections # To view all collections within a container / db
show dbs # to list all dbs on the server
```

`CRUD` operations allows us to manipulate our data:

```js
db.collection_name.insertOne({...})
db.collection_name.updateOne({...})
db.collection_name.deleteOne({...})
```

`methods` are used to run various queries on our collections:

```js
db.hello() // provides some information about the role of the mongod instance we are connected to.
db.collection.find() // Finds all documents in a collection
db.collection.findOne({...}) // Finds an specific document in a collection
```

Example: Find a document with username of samantha27 and remove the account number from the accounts array on it

```bash
mongosh "mongodb+srv://myAtlasDBUser:myatlas-001@myatlasclusteredu.7so1q.mongodb.net/sample_analytics?appName=config-mongosh" --eval "db.customers.updateOne({username: 'samantha27'}, {\$pull:{accounts: 515170}})"
```

### 3.2. Configuring the MongoDB Shell

There are three different ways to configure the MongoDB Shell:

- Set configration options by using the config API
- Change settings via the mongosh.conf
- Pass commandline arguments to mongosh to change specific settings or the return the results of a query


#### 3.2.1. Config API

- Enables us to change settings in the shell.
- We access the config API through the `config` object
- Setting will persist between shell sessions.
- Stored on a **per-user** basis in the file system

**Examples**

```js
config.get('enableTelemetry') // Get the 'enableTelemetry' setting value
config.set('enableTelemetry', false) // Sets the 'enableTelemetry' setting value to 'false'
config.reset('...') // Reset values to its default setting
```

The available config options are as follows:

- `displayBatchSize` - The number of documents to display when using the `it` iterator.
- `maxTimeMS` - The maximum amount of time to allow a query to run.
- `enableTelemetry` - Whether to enable telemetry.
- `editor` - The editor to use when editing code.
- `snippetIndexSourceURLs` - The URLs to use when fetching snippet index files.
- `snippetRegistryURL` - The URL to use when fetching snippet registry files.
- `snippetAutoload` - Whether to automatically load snippets.
- `inspectCompact` - Whether to use compact mode when inspecting objects.
- `inspectDepth` - The maximum depth to use when inspecting objects.
- `historyLength` - The number of history entries to keep.
- `showStackTraces` - Whether to show stack traces when errors occur.
- `redactHistory` - Whether to redact sensitive information from history.

#### 3.2.2. mongosh.conf

- Plaintext file
- Allows to modify the same settings that are available through the config API
- Changes are overridden if the API is used to change the same setting
- Sets options for all users of the shell
- Created by the user
- Location depends on operating system

**Example**

- The `displayBatchSize` and editor settings, which dictates how many documents are displayed per batch with the db.`collection.find()` method (20 by default).
- The `editor` command determines which context editor will open when using the edit command (vim, nano, etc).
- Windows: `mongosh.cfg`, in the same directory as the `mongosh.exe` binary.
- macOS:
 - `/usr/local/etc/mongosh.conf`
 - `/etc/mongosh.conf`
 - `/opt/homebrew/etc/mongosh.conf`
- Linux: `/etc/mongosh.conf`

```bash
sudo touch /etc/mongosh.conf
sudo cat > /etc/mongosh.conf << EOF
mongosh:
 displayBatchSize: 50
 inspectDepth: 20
 redactHistory: "remove-redact"
END
```

#### 3.2.3. commandline arguments

- using the `--eval flag`
- Passing commands or javascript code to `mongosh`
- We can return the results of a query without entering the shell
- we're using the `--nodb` flag to start the shell without requiring a connection string

**Examples**

To launch mongosh with the `enableTelemetry` setting `disabled` we can use:

```bash
mongosh --eval "disableTelemetry()"
```

To return the results of a query without entering the shell, we'll first find three documents form the CLI in a collection by passing the query to the `eval` flag. We'll also add add the --quiet flag to clean-up the output and return only the first three documents:

```bash
mongosh "mongodb://localhost:27017/sample_analytics" --eval "db.accounts.find().limit(3)" --quiet
mongosh --eval "db.accounts.find().limit(3)" --quiet
```

We can also pass variables directly to the shell via the `--eval` flag and the `--shell` flag, which tells mongosh to enable the shell interface. Passing variables this way we can print the value of the variable (hello world) by calling the variable name (hello):

```bash
mongosh --eval "var hello = 'hello world'" --shell
```

### 3.3. Using the mongoDB Shell

Key facts:

- It's important to know the abiliy of mongosh to leverage javascript
- In mongoDB is a good practice to use dates in the ISODate format

> TO-DO: CHECK THE VIDEO AGAIN & ELLABORATE

### 3.3.1. Code Summary: Using the MongoDB Shell

The following sections explain how to use `mongosh` to run external scripts and to edit commands in an external editor.

**Run External Scripts**

To run an external script in mongosh, use the load() method. For example, to run the randomPost.js file, you would run the following code:

```js
load('randomPost.js')
```

Within your script, you can use the `db.getSiblingDB()` method to access a database without having to switch to it in `mongosh`. For example, here’s how you would access the sample_training database in the `randomPost.js` script:

```js
db = db.getSiblingDB("sample_training");
console.log(`\nCurrent database: ${db.getName()}`);
console.log("Random post sample of 500 words:\n");
let result = db.posts.aggregate({
 $sample: { size: 1 },
});
printjson(result.next().body.slice(0, 500) + " ...");
```

**Edit Commands in an External Editor**

To edit a function or command in an external text editor, like Vim or nano, while using mongosh, use the edit command. To use this command, you must first set the config.editor value in mongosh. To do so, use the config.set() method:

```js
config.set("editor", "vim")
```

Once the editor is set, you can then use edit to modify a new or existing command. For example, to edit the giveMeADate function, you would run the following code:

```sh
edit giveMeADate
```

### 3.4. Using the MongoDB Shell Library (.mongoshrc.js)

This file opens up all sorts of possibilities for adding functionality and customization. For example, we can use the the `.mongoshrc.js` to:

- Add functionality to the MongoDB Shell (`mongosh`) with custom helper functions
- Add custom helper functions and customize the prompt to include additional information about our database
- On startup, mongosh checks our home directory got a hidden file called `.mongoshrc.js` and reads its contents before displaying a prompt for the first time

#### 3.4.1. Add custom helper functions

By using the `.mongoshrc.js` file we can add functionality to the MongoDB Shell (`mongosh`). For example, as a DBA we might want to check if a server is running a compatible version for a given client. For this we can use a built-in helper command called `db.adminCommand()` wich runs commands against the admin database.

 - When you specify a command that you want to run against the admin database, the command should be passed as a document. The command name should be one of the keys on the document, and in most cases its value should be set to 1.
 - To confirm this, you should always ______ the details of the command in question
 - For commands that do not require additional parameters, we take a shortcut py passing the command name as a string instead of a document to `db.adminCommand()`

   Example:

   ```js
   db.adminCommand({CommandName:1})
   ```

Now let's go back to checking the compatibilty version of the mongoDb server. This can help determininig if there will be issues with incompatible clients.

Our command starts with `db.adminCommand`. Then we pass in the command document like this:

```js
test > db.adminCommand({getParameter: 1, featureCompatibilityVersion: 1})
{featureCompatibilityVersion: { version: '5.0'}}
```
- the `getParameter` command is used to get specific values for a parameer by setting its value to 1.
- The second field is the parameter value we want to retrieve. In this case we want the `featureCompatibilityVersion`, so we also set its value to one.

After running this command, we can see that the ouput contains the `featureCompatibilityVersion` of the server

But one issue is that the output of the command is pretty loing, so lets add a helper function to the `.mongoshrc.js` called `fcv()` to act as a shortcut. This will make running the command much easier. The `.mongoshrc.js` wont exist by default, so we should create it first:

```bash
$ touch ~/.mongoshrc.js
$ vim ~/.mongoshrc.js
```

```js
const fcv = () => db.adminCommand({getParameter: 1, featureCompatibilityVersion: 1})
```

Now we can test it, but first we need to restart the `mongosh`:

```js
$ mongosh
test> fcv()
{featureCompatibilityVersion: { version: '5.0'}}
```

We can see that we get the same result as if we ran the command directly, the only difrerence is only that this time we had much less to type.


#### 3.4.2. Customize the mongosh prompt

Helper functions are just one thing that we can add to the .mongoshrc.js file. Next we will customize our prompt.

The default prompt some basic information, but we can use native methos on mongosh to get info about the mongodb server we're connected to and display it on the promt.

For example, we'll add a function to our .mongoshrc.js called prompt. In the prompt function we'll add some varialbes to get information such as the name of the db, whetener we're connectyed to atlas, the name of the database, the read `reference and the current user to name a few.

We can access this information by using the db object in mongosh. With these variables set, now al  we need to do is retiunr a string that contains the information for those variables.

```bash
$ touch ~/.mongoshrc.js
$ vim ~/.mongoshrc.js
```

```js
prompt = () => {
  let returnString = "";
  const dbName = db.getName();
  const isEnterprise = db.serverBuildInfo().modules.includes("enterprise");
  const mongoURL = db.getMongo()._uri.includes("mongodb.net");
  const nonAtlasEnterprise = isEnterprise && !mongoURL;
  const usingAtlas = mongoURL && isEnterprise;
  const readPref = db.getMongo().getReadPrefMode();
  const isLocalHost = /localhost|127\.0\.0\.1/.test(db.getMongo()._uri);
  const currentUSer = db.runCommand({ connectionStatus: 1}).authInfo.authenticatedUsers[0]?.user;
  if (usingAtlas) {
    returnString += `Atlas || ${dbName} || ${currentUser} || ${readPref} || =>`
  } else if (isLocalHost) {
    returnString += `${nonAtlasEnterprise ? "Enterprise || localhost" : "localhost" } || ${dbname} || ${readPref} || =>`
  } else if (nonAtlasEnterprise) {
    returnString += `Enterprise || ${dbname} || ${currentUser} || ${readPref} || =>`
  } else {
    returnString += `${dbname} || ${readPref} || =>`
  }
  return returnString;
}
```

After returning these modifications, we restart the mongosh connect to atlas, and see that now the prompt shows the information we specified on the prompt function.
