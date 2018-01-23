### Node.js - Guide
#### section 4 - Data Stores - MongoDB intro with native driver

A brief outline of using MongoDB and native driver with Node.js based development.

#### Contents
* intro
* basic intro to NoSQL
* install MongoDB
* running mongodb
* using MongoDB
* Robo 3T MongoDB GUI

#### intro
As we build out our Node.js apps, we can start to add persistence to the data with the addition of data stores.

This primarily includes, but is not limited to, NoSQL data stores such as MongoDB. We can then use modules such as Mongoose to help connect, query, and structure how our apps work with MongoDB.

We can also create our own custom REST APIs for Node.js based web apps, offering various routes for get, post, put, and delete.

#### data stores - basic intro to NoSQL
So, what is NoSQL? In particular, it's often useful to consider NoSQL against a familiar SQL database structure. e.g.

|  SQL Structure  | NoSQL Structure|
|:---------------:|:--------------:|
| database        |  database      |
| table - users   |  collection - e.g. a library  |
| row / records  - user  |  document - e.g. a book  |
| column - userID, name  |  field - e.g. author, title  |

#### data stores - install mongodb
Further information on MongoDB is available at the following URL,

  * https://www.mongodb.com

Direct downloads are available from this site for Windows, Linux, and OS X. Download the latest *MongoDB Community Edition* executable for your OS, and click install.

Further details on installing MongoDB can be found at the following URL,

  * https://docs.mongodb.com/manual/installation/

including OS specific help docs.

On OS X, it's also possible to install using Homebrew,

```
brew update
brew install mongodb
```

Unless specifically set, MongoDB will set a data directory for the installed DBs at `/data/db/`.

It will also use this directory to store `*.data` files for the current MongoDB install/

#### data stores - running mongodb
We can start the daemon for MongoDB using the following terminal command,

```
mongod
```

This command will output some debug information, and the final line will usually detail *network* information, including the port number waiting for connections. This means the daemon is running OK, and is now ready for connections.

With the daemon running, we can then connect to MongoDB using the following terminal command,

```
mongo
```

With the db running, and a client connected, we can then start to query, update, monitor, maintain &c. our installed MongoDB databases.

**n.b.** it's also possible to specify a custom `data` directory for local databases, e.g.

```
mongod --dbpath ~/dev-data/mongo-data
```

This would start the MongoDB daemon and specify a working data directory in the home directory.

#### data stores - using MongoDB
We can now add a DB, some initial content, query for results and so on.

For example, if we wanted to add some initial data, we can use the following command,

```
db.NodeTodo.insert({text: 'our first todo item'})
```

We can then query the `NoteTodo` DB for all current data,

```
db.NodeTodo.find()
```

This query will return the following type of output, e.g.

```json
{ "_id" : ObjectId("5970f5384aadb18eed551dc5"), "text" : "our first todo item..." }
```

This is an object, which includes a unique ID for this record, and the name:value pair we just inserted.

#### data stores - Robo 3T MongoDB GUI
We can also install a GUI, *Robo 3T*, for managing MongoDB,

  * https://robomongo.org/

Download and install the applicable package for your current OS, including Windows, Linux, and OS X,

  * https://robomongo.org/download
