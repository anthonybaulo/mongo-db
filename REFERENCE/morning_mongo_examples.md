Taken from Ryan Henning.

# MongoDB

Launch `mongod`. Explain that it is a little server.

Run `mongo --help`. Explain that it is a client that connects to a `mongod` server. Note that the default will be that it connects to 'localhost'.

Open TWO `mongo` clients (in two separate terminals).  Note that they are connected to the same 'localhost' server.

`mongo` gives us a _shell_ where we can issue commands.

In one terminal:

  - `show dbs`. That shows the database names.
  - `use demo_db`. That binds to the 'demo_db' database, creating it if it doesn't exist.
  - `db`. That's a reference to the current database ('demo_db' on our case).
  - `db.users`. That references the 'users' collection inside the current database. A collection is sorta like a table in an RDBMS.
  - `db.users.find()`. Think of the find method as SELECT from RDBMS world. It's the closest thing we have, anyway. This prints nothing because our users collection is empty.
  - `db.users.insert({})`. Insert an empty "document" (sorta like a row in an RDBMS).
  - `db.users.find()`. You should see that empty document now!

Now switch to the other terminal with the other `mongo` client. Show that our db, collection, and document show up.

Now switch back to the first terminal:

  - create a new document with: `db.users.insert({ 'name': 'Ryan', 'age': 84, 'friends': ['Scott C', 'Scott G', 'Scott S'] })`

  - see how it looks with: `db.users.find()`

  - create a new document with: `db.users.insert({ 'name': 'Scott C', 'age': 12, 'friends': ['Ryan', 'Scott G', 'Scott S'] })`

  - create a new document with: `db.users.insert({ 'name': 'Scott G', 'friends': ['Scott C', 'Ryan', 'Scott S'] })`

  - create a new document with: `db.users.insert({ 'name': 'Scott S', 'friends': ['Scott C', 'Scott G', 'Ryan'], 'fav_color': 'green' })`

  - see how it looks with: `db.users.find()`

Note how we can stick whatever we want in here. Mongo doesn't care. Mongo will *never* care.

Let's actually query for stuff:
  - `db.users.find()`

  - `db.users.find().count()`

  - `db.users.find({ 'name': 'Ryan' })`

  - `db.users.find({ 'name': /Scott/ })`

  - `db.users.findOne({ 'name': /Scott/ })`

  - `db.users.find({ 'name': /Scott/ }, { 'friends': 1 })`   <-- 'inclusion query'

  - `db.users.find({ 'name': /Scott/ }, { friends: 0 ,_id : false})`   <-- 'exclusion query'

  - `db.users.find({friends: {$exists: true}},{friends: true, _id: false})`

  - `db.users.find({ 'age': {'$lt': 50} })`

  - `db.users.find({ 'age': {'$lt': 50, '$gt': 20} })`



  - `db.users.update({name: 'Bob'}, {$set : {age : 33, fav_color : 'green'}}, {multi : true})`

  - `db.users.update({name: 'Bob'}, {$set : {age : 33, fav_color : 'green'}}, {multi : true, upsert : true})`


  - `db.users.update({ 'name': /Scott/ }, {'$set': {'gender': 'male'} })`

  -
