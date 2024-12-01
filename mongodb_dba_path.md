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

#### 2.3.1. Embedding

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

## 3. The MongoDB Shell

> This information was obtained from https://learn.mongodb.com/learn/course/the-mongodb-shell



In this unit, we'll learn how to:

- Install the MongoDB Shell (mongosh) and connect to self-hosted and Atlas deployments
- Configure mongosh by using the config API and setting options in the configuration file (mongosh.conf)
- Pass command line arguments to mongosh
- Write a JavaScript function inside mongosh
- Use db.getSiblingDb() to change databases within a script by using the MongoDB Shell's load() method
- Edit a function or variable from mongosh in an external text editor
- Add a helper function to .mongoshrc.js to add functionality to the MongoDB Shell
- Add information to the mongosh prompt by editing the .mongoshrc.js file

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

#### 3.3.1. Code Summary: Using the MongoDB Shell

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
 const currentUser = db.runCommand({ connectionStatus: 1 }).authInfo
   .authenticatedUsers[0]?.user;
 if (usingAtlas) {
   returnString += `Atlas || ${dbName} || ${currentUser} || ${readPref} || =>`;
 } else if (isLocalHost) {
   returnString += `${
     nonAtlasEnterprise ? "Enterprise || localhost" : "localhost"
   } || ${dbName} || ${readPref} || =>`;
 } else if (nonAtlasEnterprise) {
   returnString += `Enterprise || ${dbName} || ${currentUser} || ${readPref} || =>`;
 } else {
   returnString += `${dbName} || ${readPref} || =>`;
 }
 return returnString;
};
```

After returning these modifications, we restart the mongosh connect to atlas, and see that now the prompt shows the information we specified on the prompt function.

### 3.5. MongoDB Shell Tips and Tricks

The following sections explain some tips and tricks for using mongosh.

#### 3.5.1. Use Node.js APIs to Read and Write Files

`mongosh` provides access to all native Node.js APIs, including the file system module, or `fs`. This means that you can use `mongosh` to read and write files. In the following example, we assign the result of a query to a variable called `customers` so that it can be used later:

```js
const customers = db.sales.find({}, {customer: 1, _id: 0})
```

Next, we use the `fs` module to write the results of the query to a file called `customers.json`. To do this, we pass in the customers variable and use the `EJSON.stringify()` method to convert the array to a string. We also use the `null` and `2` parameters to format the output so that it’s easier to read:

```sh
fs.writeFileSync('customers.json', EJSON.stringify(customers.toArray()), null, 2)
```

#### 3.5.2. Generate Seed Data

You can also use `npm` packages in `mongosh`, as they support external scripts and `require` statements. In the following example, we use the faker package to generate an array of 10 fake users. We then insert the users as documents into a new database.

```js
const { faker } = require("@faker-js/faker");
const users = [];
for (let i = 0; i < 10; i++) {
 users.push({
   name: faker.person.fullName(),
   email: faker.internet.email(),
   phone: faker.phone.number(),
 });
}

console.log("Inserting fake users ...");
db.getSiblingDB("test_data").users.insertMany(users);
```

**Note:** To use the faker package, you must first install it by using `npm install @faker-js/faker --save-dev` in the same directory as your external script. Or, you can install the package globally by using `npm install -g @faker-js/faker`.

### 3.6. Complementary resources

****Table of Contents**

Use the following resources to learn more about how to install, use, and customize the MongoDB Shell, or mongosh.

**General**

- [Getting Started with MongoDB Atlas](https://learn.mongodb.com/courses/getting-started-with-mongodb-atlas)
- [mongodb-js/mongosh (GitHub)](https://github.com/mongodb-js/mongosh)

**Lesson 1: Installing and Connecting to the MongoDB Shell**

- [Install mongosh](https://www.mongodb.com/docs/mongodb-shell/install/)
- [Connect to a Deployment](https://www.mongodb.com/docs/mongodb-shell/connect/)
- [Connect to a Database Deployment (for Atlas)](https://www.mongodb.com/docs/atlas/connect-to-database-deployment/)
- [MongoDB Shell Options](https://www.mongodb.com/docs/mongodb-shell/reference/options/)
  - [Host](https://www.mongodb.com/docs/mongodb-shell/reference/options/#std-option-mongosh.--host)
  - [Port](https://www.mongodb.com/docs/mongodb-shell/reference/options/#std-option-mongosh.--port)
  - [Username](https://www.mongodb.com/docs/mongodb-shell/reference/options/#std-option-mongosh.--username)

**Lesson 2: Configuring the MongoDB Shell**

- [Configure mongosh](https://www.mongodb.com/docs/mongodb-shell/configure-mongosh/)
- [Configure Settings Using the API](https://www.mongodb.com/docs/mongodb-shell/reference/configure-shell-settings-api/#std-label-configure-settings-api)
- [Configure Settings Using a Configuration File](https://www.mongodb.com/docs/mongodb-shell/reference/configure-shell-settings-global/#std-label-configure-settings-global)
- [Configure Settings](https://www.mongodb.com/docs/mongodb-shell/reference/configure-shell-settings/#std-label-mongosh-shell-settings)

**Lesson 3: Using the MongoDB Shell**

- [Write Scripts](https://www.mongodb.com/docs/mongodb-shell/write-scripts/)
- [db.getSiblingDB()](https://www.mongodb.com/docs/manual/reference/method/db.getSiblingDB/)
- [Use an Editor for Commands](https://www.mongodb.com/docs/mongodb-shell/reference/editor-mode/)
- [Gist for randomPost.js](https://gist.github.com/nol166/298d1c0f02fb1837f546e16163e097f5)
- [Gist for giveMeADate.js](https://gist.github.com/nol166/16565bcf9705e086aac6e80a44254e16)

**Lesson 4: Using the MongoDB Shell Library (.mongoshrc.js)**

- [Run Code From a Configuration File](https://www.mongodb.com/docs/mongodb-shell/write-scripts/#std-label-mongosh-write-scripts-config-file)
- [Customize the mongosh Prompt](https://www.mongodb.com/docs/mongodb-shell/reference/customize-prompt/#std-label-customize-the-mongosh-prompt)
- [Gist for featureCompatibityVersion Helper (.mongoshrc.js)](https://gist.github.com/nol166/63d84e25f4de0a27a0e0b93c284f8295)
- [Gist for Additional Information in the mongosh Prompt (.mongoshrc.js)](https://gist.github.com/nol166/c93e1b52b31488f7a17b50e0f66114ea)

**Lesson 5: MongoDB Shell Tips and Tricks**

- [MongoDB Shell Tips and Tricks](https://dev.to/mongodb/mongodb-shell-tips-and-tricks-1ceg)
- [writeFileSync() (Node.js Documentation)](https://nodejs.org/api/fs.html#fswritefilesyncfile-data-options)
- [Faker npm Package](https://fakerjs.dev/guide/)
- [load() in mongosh](https://www.mongodb.com/docs/v5.2/reference/method/load/)
- [require() in mongosh](https://www.mongodb.com/docs/mongodb-shell/write-scripts/require-external-modules/#require-a-local-file)

## 4. Connecting to a MongoDB Database

In this unit, you will learn where to find your Atlas connection string inside of the Atlas dashboard. You'll also learn about the components of a Atlas connection string. Finally, you will have the opportunity to use your connection string to connect your Atlas cluster with the shell, Compass, and any type of application.

### 4.1 Using MongoDB Connection Strings

#### 4.1.1. Introduction

In this lesson we'll learn:

- How to use the mongoDB connection string
- Where locate the mongoDB connection string for an Atlas cluster
- What the string consists of

The mongoDB connection string allows us to connect to our cluster and work with our data. It describes a host that we'll be using and the options for connecting to a mongoDB database. For example, the con string can be used to connect from:

- The mongoDB shell
- MongoDB Compass
- Any other application

#### 4.1.2. Connection string formats

MongoDB provides 2 formats for the connection string:

- **A standard format:** Used to connect to standalone clusters, replica sets, or sharded clusters
- **A DNS seed list format:** Was released in mongoDB 3.6 and allows us to provide a DNS server list to our connection stream, which gives us a lot more flexibility with our deployment and the ability to change servers in our rotation without reconfiguring any of our clients. It's used by the connection string from the Atlas dashboard and has a list of hosts we can connect to.

#### 4.1.3. Connection String components

At first glance, the connection string appears to be a very long string of characters, but we can break it down into several components:

```bash
mongodb+srv://<username>:<password>@cluster0.usqsf.mongodb.net/?retryWrites=true&w=majority
```

- **"mongodb" required prefix:** It identifies it as a mongoDB connection string.
- **"srv" edition:** Automatically sets the TLS security option to true and tells MongoDB to use the DNS seed list.
- **username & password:** Following the MongoDB prefix, there's the username and password that we created for our database in the Atlas dashboard
- **Host and optional port number:** If the port number is not specified, MongoDB will default to port 27017
 **Options:** The final piece of the connection string includes any options we want to include, such as:
  - Connection timeout
  - TLS/SSL
  - Connection pooling
  - R/W concerns
  - other options like "retryWrites", which tells MongoDB drivers to automatically retry when certain types of write operations fail (MongoDB has options for just about anything we may need when it comes out to connecting to a database).

### 4.2. Connecting to a MongoDB Atlas Cluster with the Shell

In this lesson we'll learn to connect to our cluster via the MongoDB shell using our application string. We'll also larn about the node.js `REPL` (Read-Eval-Print-Loop) environment that's used by the MongoDB shell.

The MongoDB shell is a Node.js `REPL` environment. This gives us access to the following mechanisms from within the shell:

- JavaScript variables
- Functions
- Conditionals
- Loops
- Control flow statements

For example, we'll create a variable for an array of strings:

```js
prompt> const GreetingArray = ["Hello","World","Welcome"];
```

Now we'll create another variable for a function that will loop over the array variable and print-out each one of the elements.

```js
prompt> const loopArray = (array) => array.forEach(el => console.log(el));
```

Now we can use the loopArray function with our greetingArray as the argument to console out the elements of the array:

```js
prompt> loopArray(greetingArray);
```

Summing-up, we can definitely leverage many of the elements from javascript in the mongoDB shell.

### 4.3. Connecting to a MongoDB Atlas Cluster with Compass

In this lesson we'll learn how to use a connection string with MongoDB Compass to connect to our cluster. We'll also take a quick tour of Compass and show how to use it to view and interact with our data.

MongoDB Compass is a graphical user interface or GUI that allows us to query and analyze our data, and compose aggregation pipelines.

We can use our connection string with Compass to connect to an Atlas cluster

### 4.4. Connecting to a MongoDB Atlas Cluster from an Application

In this lesson we'll learn about MongoDB drivers, as well as where to find the documentation we need to use these drivers.

MongoDB drivers allow an application to connect to the database using the programming language of our choice.

Our driver uses a connection string to connect to our cluster. For a list of languages supported by MongoDB we can go to https://mongodb.com/docs/drivers

### 4.5. Troubleshooting MongoDB Atlas Connection Errors

In this lesson we're going to learn how to troubleshoot two of the most common connection errors. These errors can cause frustration for many developers, but they are fairly easy to diagnose and fix. We'll cover what causes these errors, how they behave, and how to fix them.

Connection issues area a failure of our application to connect to our mongoDB database, and most of them stem from barriers put up by MongoDB to secure our data.

#### 4.5.1. Network access errors

**IP Not allowed Error:**

```js
MongoServerSelectionError: connection <monitor> to IP:27017 closed
```

**Fix:**

We can whitelist either temporarily or permanently an IP address to Atlas in the `security > Network` access list

#### 4.5.2. User authentication errors

**Authentication failed error:**

MongoDB does not autofill our password filed in the connection string, so make sure the password is populated and correct before connecting.

```js
MongoServerError: bad auth : Authentication failed.
```

> Notice that an authentication error might be triggered if any other part of the connection string was not properly typed, so make sure the connection string is exactly what we copied from the Atlas website.

### 4.6. Resources

Resources

Use the following resources to learn more about connecting to your database.

**Lesson 01: Using MongoDB Connection Strings**

- MongoDB Docs: [Get Connection String](https://www.mongodb.com/docs/guides/atlas/connection-string/?_ga=2.2826361.818394043.1666026366-33697719.1666026366)
- MongoDB Docs: [Connection String URI Format](https://www.mongodb.com/docs/manual/reference/connection-string/)

**Lesson 02: Connecting to a MongoDB Atlas Cluster with the Shell**

- MongoDB Docs: [The MongoDB Shell](https://www.mongodb.com/docs/mongodb-shell/)

**Lesson 03: Connecting to a MongoDB Atlas Cluster with Compass**

- MongoDB Docs: [MongoDB Compass](https://www.mongodb.com/products/compass/)

**Lesson 04: Connecting to a MongoDB Atlas Cluster from an Application**

- MongoDB Docs: [Connect via Your Application](https://www.mongodb.com/docs/atlas/driver-connection/)

**Lesson 05: Troubleshooting MongoDB Atlas Connection Errors**

- MongoDB Docs: [Troubleshoot Connection Issues](https://www.mongodb.com/docs/atlas/troubleshoot-connection/)

## 5. MongoDB CRUD Operations: Insert and Find Documents

**Unit Overview**

In this unit, you will be introduced to `CRUD` operations in MongoDB by inserting and finding documents. Inserting and finding documents will help you discover the ease and usability of MongoDB. You'll also build your own queries that use comparison and logical operators. Using operators will make your queries more precise and, in turn, make your application easier to develop. Finally, you'll learn how to query elements in an array. Arrays are a crucial data type that you will encounter frequently, so it's important that you have a solid understanding of how to work with them.

### 5.1. Inserting Documents in a MongoDB Collection

The following code demonstrates how to insert a single document and multiple documents into a collection.

#### 5.1.1. Insert a Single Document

Use `insertOne()` to insert a document into a collection. Within the parentheses of `insertOne()`, include an object that contains the document data. Here's an example:

```js
db.grades.insertOne({
  student_id: 654321,
  products: [
    {
      type: "exam",
      score: 90,
    },
    {
      type: "homework",
      score: 59,
    },
    {
      type: "quiz",
      score: 75,
    },
    {
      type: "homework",
      score: 88,
    },
  ],
  class_id: 550,
})
```

#### 5.1.2. Insert Multiple Documents

Use `insertMany()` to insert multiple documents at once. Within `insertMany()`, include the documents within an array. Each document should be separated by a comma. Here's an example:

```js
db.grades.insertMany([
  {
    student_id: 546789,
    products: [
      {
        type: "quiz",
        score: 50,
      },
      {
        type: "homework",
        score: 70,
      },
      {
        type: "quiz",
        score: 66,
      },
      {
        type: "exam",
        score: 70,
      },
    ],
    class_id: 551,
  },
  {
    student_id: 777777,
    products: [
      {
        type: "exam",
        score: 83,
      },
      {
        type: "quiz",
        score: 59,
      },
      {
        type: "quiz",
        score: 72,
      },
      {
        type: "quiz",
        score: 67,
      },
    ],
    class_id: 550,
  },
  {
    student_id: 223344,
    products: [
      {
        type: "exam",
        score: 45,
      },
      {
        type: "homework",
        score: 39,
      },
      {
        type: "quiz",
        score: 40,
      },
      {
        type: "homework",
        score: 88,
      },
    ],
    class_id: 551,
  },
])
```

### 5.2. Finding Documents in a MongoDB Collection

The following code demonstrates how to query documents in MongoDB.

#### 5.2.1. Find a Document with Equality

When given equality with an `_id` field, the `find()` command will return the specified document that matches the `_id`. Here's an example:

```js
db.zips.find({ _id: ObjectId("5c8eccc1caa187d17ca6ed16") })
```

#### 5.2.2. Find a Document by Using the $in Operator

Use the `$in` operator to select documents where the value of a field equals any value in the specified array. Here's an example:

```js
db.zips.find({ city: { $in: ["PHOENIX", "CHICAGO"] } })
```


### 5.3. Finding Documents by Using Comparison Operators

These are the most basic comparison operators: `$gt`, `$lt`, `$lte`, and `$gte`.

#### 5.3.1. Greater than ($gt)

Use the `$gt` operator to match documents with a field **greater than** the given value. For example:

```js
db.sales.find({ "items.price": { $gt: 50}})
```

#### 5.3.2. Less than ($lt)

Use the `$lt` operator to match documents with a field **less than** the given value. For example:

```js
db.sales.find({ "items.price": { $lt: 50}})
```

#### 5.3.3.  Less than or equal ($lte)

Use the `$lte` operator to match documents with a field **less than or equal** to the given value. For example:

```js
db.sales.find({ "customer.age": { $lte: 65}})
```

#### 5.3.4. Greater than or equal ($gte)

Use the `$gte` operator to match documents with a field **greater than or equal** to the given value. For example:

```js
db.sales.find({ "customer.age": { $gte: 65}})
```

### 5.4. Querying on Array Elements in MongoDB

The following code demonstrates how to query array elements in MongoDB.

#### 5.4.1. Find Documents with an Array That Contains a Specified Value

In the following example, "InvestmentFund" is not enclosed in square brackets, so MongoDB returns all documents within the `products` array that contain the specified value. The query may return either scalar elements that contain this value, or arrays with any of its elements vaule matches the queried value.

```js
db.accounts.find({ products: "InvestmentFund"})
```

#### 5.4.2. Find a Document by Using the $elemMatch Operator

We use the `$elemMatch` operator to find all documents that contain the specified subdocument. For example:

```js
db.sales.find({
  items: {
    $elemMatch: { name: "laptop", price: { $gt: 800 }, quantity: { $gte: 1 } },
  },
})
```
### 5.5. Finding Documents by Using Logical Operators

Review the following logical operators: implicit `$and`, `$or`, and `$and`.

#### 5.5.1. Find a Document by Using Implicit $**and**

Use implicit $and to select documents that match multiple expressions. For example:

```js
db.routes.find({ "airline.name": "Southwest Airlines", stops: { $gte: 1 } })
```

#### 5.5.2. Find a Document by Using the $or Operator

Use the $or operator to select documents that match at least one of the included expressions. For example:

```js
db.routes.find({****
  $or: [{ dst_airport: "SEA" }, { src_airport: "SEA" }],
})
```

#### 5.5.3. Find a Document by Using the $and Operator

Use the $and operator to use multiple $or expressions in your query.

```js
db.routes.find({
  $and: [
    { $or: [{ dst_airport: "SEA" }, { src_airport: "SEA" }] },
    { $or: [{ "airline.name": "American Airlines" }, { airplane: 320 }] },
  ]
})
```

### 5.6. Resources

Use the following resources to learn more about inserting and finding documents in MongoDB:

**Lesson 1 - Inserting Documents**

- MongoDB Docs: [insertOne()](https://docs.mongodb.com/manual/reference/method/db.collection.insertOne/)
- MongoDB Docs: [insertMany()](https://docs.mongodb.com/manual/reference/method/db.collection.insertMany/)

**Lesson 2 - Finding Documents**

- MongoDB Docs: [find()](https://docs.mongodb.com/manual/reference/method/db.collection.find/)
- MongoDB Docs: [$in](https://docs.mongodb.com/manual/reference/operator/query/in/)

**Lesson 3 - Finding Documents Using Comparison Operators**

- MongoDB Docs: [Comparison Operators](https://docs.mongodb.com/manual/reference/operator/query-comparison/)

**Lesson 4 - Querying on Array Elements**

- MongoDB Docs: [$elemMatch](https://docs.mongodb.com/manual/reference/operator/query/elemMatch/)
- MongoDB Docs: [Querying Arrays](https://docs.mongodb.com/manual/tutorial/query-array-of-documents/#combination-of-elements-satisfies-the-criteria)

**Lesson 5 - Finding Documents Using Logical Operators**

- MongoDB Docs: [Logical Operators](https://docs.mongodb.com/manual/reference/operator/query-logical/)

## 6. MongoDB CRUD Operations: Replace and Delete Documents

In this unit, we'll learn how to update, replace, and delete documents in MongoDB. These commands will help you manipulate data in your database and will prepare you to build MongoDB into your own apps. We will replace entire documents, update individual fields in a document, insert new documents, and remove documents from a database. By the end of this unit, you'll be able to execute most of the common database operations.

### 6.1. Replacing a Document in MongoDB

To replace documents in MongoDB, we use the `replaceOne()` method. The `replaceOne()` method takes the following parameters:

- `filter`: A query that matches the document to replace.
- `replacement`: The new document to replace the old one with.
- `options`: An object that specifies options for the update.

In the next example, we use the `_id` field to filter the document. In our replacement document, we provide the entire document that should be inserted in its place:

```js
db.books.replaceOne(
  {
    _id: ObjectId("6282afeb441a74a98dbbec4e"),
  },
  {
    title: "Data Science Fundamentals for Python and MongoDB",
    isbn: "1484235967",
    publishedDate: new Date("2018-5-10"),
    thumbnailUrl:
      "https://m.media-amazon.com/images/I/71opmUBc2wL._AC_UY218_.jpg",
    authors: ["David Paper"],
    categories: ["Data Science"],
  }
)
```

### 6.2. Updating MongoDB Documents by Using updateOne()

The `updateOne()` method accepts a filter document, an update document, and an optional options object. MongoDB provides update operators and options to help you update documents. In this section, we'll cover three of them: `$set`, `upsert`, and `$push`.


#### 6.2.1. Replaces the value of a field with the specified value ($set)

The `$set` operator replaces the value of a field with the specified value, as shown in the following code:

```js
db.podcasts.updateOne(
  {
    _id: ObjectId("5e8f8f8f8f8f8f8f8f8f8f8"),
  },
  {
    $set: {
      subscribers: 98562,
    },
  }
)
```

#### 6.2.2. Create a new document if no documents match the filtered criteria (upsert)

The `upsert` option creates a new document if no documents match the filtered criteria. Here's an example:

```js
db.podcasts.updateOne(
  { title: "The Developer Hub" },
  { $set: { topics: ["databases", "MongoDB"] } },
  { upsert: true }
)
```

#### 6.2.3. Add a new value to the hosts array field ($push)

The `$push` operator adds a new value to the hosts array field. Here's an example:

```js
db.podcasts.updateOne(
  { _id: ObjectId("5e8f8f8f8f8f8f8f8f8f8f8") },
  { $push: { hosts: "Nic Raboy" } }
)
```

### 6.3. Updating MongoDB Documents by Using findAndModify()

The `findAndModify()` method is used to find and replace a single document in MongoDB. It accepts a filter document, a replacement document, and an optional options object. The following code shows an example:

```js
db.podcasts.findAndModify({
  query: { _id: ObjectId("6261a92dfee1ff300dc80bf1") },
  update: { $inc: { subscribers: 1 } },
  new: true,
})
```

### 6.4. Updating MongoDB Documents by Using updateMany()

To update multiple documents, use the `updateMany()` method. This method accepts a **filter document**, an **update document**, and an optional **options object**. The following code shows an example:

```js
db.books.updateMany(
  { publishedDate: { $lt: new Date("2019-01-01") } },
  { $set: { status: "LEGACY" } }
)
```

> **When to use `updateMany()`:**
>
> - Is not an all-or-nothing operation (**is not atomic**), which means that if an operation fails, the method will not rollback the updates, so only some documents might be updated. If that happens, we might have to run the method again.
> - `updateMany()` **lacks isolation**, which means that updates will be visible as soon as they are performed. Because of this, this method is not appropiate for some use cases that may be essential for business requirements, such as financial transactions.

#### 6.4.1. Lab practice

##### Updating Multiple Documents

Imagine you’re working with a research who asked you to update the last_seen field value of the `birds` collection for a few different species at once.

Before you begin, please note that you are now connected to an Atlas cluster and the `bird_data` database. Use the `birds` collection for this lab.

**Lab Instructions**

1. Update the last_seen date to 2022-01-01 for Blue Jay and Grackle in the birds collection.

```js
bird_data> db.birds.updateMany(
	{ common_name: { $in: ["Blue Jay", "Grackle"] }},
  { $set: { last_seen: new ISODate("2022-01-01") }})

{
  acknowledged: true,
  insertedId: null,
  matchedCount: 2,
  modifiedCount: 2,
  upsertedCount: 0
}

db.birds.find({common_name: {$in: ["Blue Jay","Grackle"]}})
[
  {
    _id: ObjectId('6286a5612f3fa87b7d86dcd2'),
    common_name: 'Grackle',
    scientific_name: 'Quiscalus quiscula',
    wingspan_cm: 28.04,
    habitat: 'pine trees',
    diet: [ 'insects', 'minnows', 'eggs' ],
    sightings_count: 7,
    last_seen: ISODate('2022-05-19T20:20:44.083Z')
  },
  {
    _id: ObjectId('628682d92f3fa87b7d86dcce'),
    common_name: 'Blue Jay',
    scientific_name: 'Cyanocitta cristata',
    wingspan_cm: 34.17,
    habitat: 'forests',
    diet: [ 'vegetables', 'nuts', 'grains' ],
    sightings_count: 4,
    last_seen: ISODate('2022-05-19T20:20:44.083Z')
  }
]
```

### 6.5. Deleting Documents in MongoDB

To delete documents, we use the `deleteOne()` or `deleteMany()` methods. Both methods accept a filter document and an options object.

#### 6.5.1. Delete One Document

The following code shows an example of the `deleteOne()` method:

```js
db.podcasts.deleteOne({ _id: Objectid("6282c9862acb966e76bbf20a") })
```

#### 6.5.2. Delete Many Documents

The following code shows an example of the `deleteMany()` method:

```js
db.podcasts.deleteMany({category: "crime"})
```

#### 6.5.3. Lab practice

##### Delete a Single Document

In this activity we will delete one document from the `birds` collection.

Before you begin, please note that you are now connected to an Atlas cluster and the `bird_data` database. Use the `birds` collection for this lab.

**Lab Instructions**

1. Delete one document from the birds collection that matches the id `ObjectId("62cddf53c1d62bc45439bebf")`

```js
db.birds.deleteOne({_id: ObjectId("62cddf53c1d62bc45439bebf")})

{ acknowledged: true, deletedCount: 1 }
```

##### Delete Multiple Documents

In this activity we will delete multiple documents from the `birds` collection.

Before you begin, please note that you are now connected to an Atlas cluster and the `bird_data` database. Use the `birds` collection for this lab.

**Lab Instructions**

1. Delete all documents in the `birds` collection that have a `sightings_count` of less than or equal to `10`.

```js
db.birds.deleteMany({sightings_count: {$lte: 10} })

{ acknowledged: true, deletedCount: 3 }
```

### 6.6. Conclusion

#### 6.6.1. Summary

In this unit, you learned how to modify query results with MongoDB. Specifically, you:

- Replaced a single document by using `db.collection.replaceOne()`.
- Updated a field value by using the `$set` update operator in db.`collection.updateOne()`.
- Added a value to an array by using the $push update operator in db.`collection.updateOne()`.
- Added a new field value to a document by using the upsert option in `db.collection.updateOne()`.
- Found and modified a document by using `db.collection.findAndModify()`.
- Updated multiple documents by using `db.collection.updateMany()`.
- Deleted a document by using `db.collection.deleteOne()`.

#### 6.6.2. Resources

Use the following resources to learn more about modifying query results in MongoDB:

**Lesson 01: Replacing a Document in MongoDB**

- MongoDB Docs: [replaceOne()](https://www.mongodb.com/docs/manual/reference/method/db.collection.replaceOne/?_ga=2.56665699.810066485.1665291537-836515500.1666025886)

**Lesson 02: Updating MongoDB Documents by Using `updateOne()`**

- MongoDB Docs: [Update Operators](https://www.mongodb.com/docs/manual/reference/operator/update/?_ga=2.56665699.810066485.1665291537-836515500.1666025886)
- MongoDB Docs: [$set](https://docs.mongodb.com/manual/reference/operator/update/set/?_ga=2.56665699.810066485.1665291537-836515500.1666025886)
- MongoDB Docs: [$push](https://docs.mongodb.com/manual/reference/operator/update/push/?_ga=2.34644840.810066485.1665291537-836515500.1666025886)
- MongoDB Docs: [upsert](https://www.mongodb.com/docs/drivers/node/current/fundamentals/crud/write-operations/upsert/?_ga=2.123127490.810066485.1665291537-836515500.1666025886)

**Lesson 03: Updating MongoDB Documents by Using `findAndModify()`**

- MongoDB Docs: [findAndModify()](https://www.mongodb.com/docs/manual/reference/method/db.collection.findAndModify/?_ga=2.123127490.810066485.1665291537-836515500.1666025886)

**Lesson 04: Updating MongoDB Documents by Using `updateMany()`**

- MongoDB Docs: [updateMany()](https://www.mongodb.com/docs/manual/reference/method/db.collection.updateMany/?_ga=2.123127490.810066485.1665291537-836515500.1666025886)

**Lesson 05: Deleting Documents in MongoDB**

- MongoDB Docs: [deleteOne()](https://www.mongodb.com/docs/v5.3/reference/method/db.collection.deleteOne/)
- MongoDB Docs: [deleteMany()](https://www.mongodb.com/docs/v5.3/reference/method/db.collection.deleteMany/?_ga=2.23103219.810066485.1665291537-836515500.1666025886)

## 7. MongoDB CRUD Operations: Modifying Query Results

In this unit, we learn how to modify query results in MongoDB by using sorts, limits, projections, and counts. First, you will learn how to organize query results by sorting and limiting the documents that are returned. Then you'll explore how to use projection to return selected fields from a query. Finally, you’ll learn how to count the number of documents that match a query. Using these query modifications will help enhance the functionality and performance of your applications.

### 7.1. Sorting and Limiting Query Results in MongoDB

The following code demonstrates how to sort and limit query results.

#### 7.1.1. Sorting Results

Use `cursor.sort()` to return query results in a specified order. Within the parentheses of `sort()`, include an object that specifies the field(s) to sort by and the order of the sort. Use `1` for **ascending order**, and `-1` for `descending order`.

**Syntax:**

```js
db.collection.find(<query>).sort(<sort>)
```

**Example:**

```js
// Return data on all music companies, sorted alphabetically from A to Z.
db.companies.find({ category_code: "music" }).sort({ name: 1 });
```

To ensure documents are returned in a **consistent order**, include a field that contains unique values in the sort. An easy way to do this is to include the `_id` field in the sort. Here's an example:

```js
// Return data on all music companies, sorted alphabetically from A to Z. Ensure consistent sort order
db.companies.find({ category_code: "music" }).sort({ name: 1, _id: 1 });
```

#### 7.1.2. Limiting Results


Use `cursor.limit()` to specify the maximum number of documents the cursor will return. Within the parentheses of `limit()`, specify the maximum number of documents to return.

**Syntax:**

```js
db.companies.find(<query>).limit(<number>)
```

**Example:**

```js
// Return the three music companies with the highest number of employees. Ensure consistent sort order.
db.companies
  .find({ category_code: "music" })
  .sort({ number_of_employees: -1, _id: 1 })
  .limit(3);
```

#### 7.1.3. Lab Practice

##### Return Query Results in Ascending Order

In this activity, you will return query results in ascending order.

**Lab Instructions**

You are now connected to an Atlas cluster and the `sample_supplies` database.

You will use the `sales` collection. (Suggestion: run `db.sales.findOne()` to return a sample document and review the structure of the data in this this collection.)

1. Return the data on all sales, ordered by date from oldest to newest.

```js
db.sales.find().sort({saleDate:1});
```

##### Return Query Results in Descending Order

In this activity, you will return query results in descending order.

**Lab Instructions**

You are now connected to an Atlas cluster and the `sample_supplies` database. You will use the `sales` collection.

1. Return the data on all sales made online using a coupon, ordered by date from the most recent to the oldest.

```js
db.sales.find({couponUsed: true}).sort({saleDate:-1})
```

##### Return a Limited Number of Sorted Results

In this activity, you will return query results sorted by most recent and specify the maximum number of documents to return.

**Lab Instructions**

You are now connected to an Atlas cluster and the `sample_supplies` database. You will use the `sales` collection.

1. Return the data on the three most recent sales made from the London store that included one or more of the following items: a laptop, a backpack or printer paper.

```js
db.sales.find({
  storeLocation: "London",
  "items.name": {$in: ["laptop","backpack","printer paper"]}
}).sort({saleDate: 1}).limit(3);
```

### 7.2. Returning Specific Data from a Query in MongoDB
