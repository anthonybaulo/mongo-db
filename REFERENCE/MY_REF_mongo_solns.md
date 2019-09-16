### Resources and quick references to Mongo commands:

   - [MongoDB Cheatsheet](https://blog.codecentric.de/files/2012/12/MongoDB-CheatSheet-v1_0.pdf)
   - [Mongo Docs](https://docs.mongodb.com/v2.2/tutorial/getting-started-with-the-mongo-shell/)

### Additional GUI clients

Here are some additional GUI clients if you so want to try (my favorite is RoboMongo):
- [Robomongo (Multiplatform)](http://robomongo.org/)
- [MongoHub (Mac OSX)](https://github.com/fotonauts/MongoHub-Mac) 
   with down-loadable [binary](https://mongohub.s3.amazonaws.com/MongoHub.zip)
- [Humongous (web based)](https://github.com/bagwanpankaj/humongous)

[mongo-download]:http://www.mongodb.org/downloads?_ga=1.2370361.886345798.1422741448
[mongo-install]:https://docs.mongodb.org/master/tutorial/install-mongodb-on-os-x/
[mongo-cheatsheet]:https://blog.codecentric.de/files/2012/12/MongoDB-CheatSheet-v1_0.pdf
[mongo-docs]:http://docs.mongodb.org/v2.2/mongo/
[mongo-ref-cards]:http://semantic.supelec.fr/BigData/downloads/MongoDB-ReferenceCards15.pdf
[mongo-like-query]:http://stackoverflow.com/questions/3305561/how-to-query-mongodb-with-like
[mongo-update]:http://docs.mongodb.org/manual/reference/operator/update/set/
[mongo-aggregation]:http://docs.mongodb.org/manual/reference/sql-aggregation-comparison/
[mongo-geospatial]:http://docs.mongodb.org/manual/administration/indexes-geo/ 
[New-England]:http://en.wikipedia.org/wiki/New_England
[torque-map]:http://blog.cartodb.com/post/66687861735/torque-is-live-try-it-on-your-cartodb-maps-today

### Using Mongo with Docker
It is highly recommended you get used to using Docker.  See [Using Mongo with Docker](using_mongo_with_docker.md) for detailed instructions.  If you want install MongoDB see instructions at the end of the assignment.  Again this is not recommended. 

### AWS MongoDB Installation
You should already have a local Mongo docker container.  Let's practice our AWS skills by spinning up a micro instance and practice installing on a remote machine.

1. To install MongoDB, use your operating system's package manager:
   - Ubuntu Linux: `sudo apt-get install mongodb`

2. Much like Postgres, you will need to launch the server before using Mongo for the first time. 

   - Ubuntu Linux: `sudo /etc/init.d/mongodb start` 

3. Check your installation by opening the MongoDB Client:

   - Open a new terminal and type `mongo` to open up a Mongo shell
   - Type `show dbs;` to show the databases you have
   - You can exit by typing `exit`



# Solutions to Mongo Exercise

If you are not sure about what command to use, you can access the help section by:
    - `help`
    - `db.help()`
    - `db.<collection_name>.help()`

1. Open a bash terminal in Docker, navigate to the directory containing the data in Docker and load in the data with

   `mongoimport --db clicks --collection log < click_log.json`

2. In the Mongo shell on Docker, run `show dbs;` to make sure the `clicks` database has 
   been created. Run `use clicks;` to use the `clicks` database for your 
   queries.
   
   `use clicks;`

3. Count the number of entries in the log table

   `db.log.count() #3069`

4. Print the first 10 entries

   `db.log.find().limit(10)`

5. Count the number of entries with the city `San Francisco`

   `db.log.find({cy: 'San Francisco'}).count()`

6. Count the distinct types of web browser (field 'a').

   `db.log.distinct('a').length`

7. Select and count the records where the users have visited a website either 
   from a `Mozilla` or an `Opera` web browser. Search the `a` field using 
   [regex in mongo][mongo-like-query]. 
   
   ` db.log.find({$or: [{'a': /Mozilla/}, {'a': /Opera/}]}, {'a': 1}).count()`


8. Find the type of the `t` (timestamp) field. You can access the type of a 
   field in an entry by using `typeof db.log.findOne({'t': {$exists: true}}).t`. 
   The field should be a `number` now.
   
   Convert the timestamp field to the date type. You will need to multiply the  number by 1000 and then make it a `Date` object (you can create a `Date` object by using `new Date()`). You can loop over each record using `.forEach()` and then [`.update()`][mongo-update] the record (using the `_id` field) with the created `Date` object. When you're done, confirm that the data type has been converted.


   ```
   db.log.find({'t': {$exists: true}}).forEach(function(entry) {
    db.log.update({_id: entry._id}, {
                $set: {'t': new Date(entry.t * 1000)}
    })
   })

   # Check type
   typeof db.log.findOne({'t': {$exists: true}}).t
   ```

9. Sort the clicks by the timestamp and find when the first click occurred. 
   How many clicks occurred in the first hour? To answer this, assign the 
   earliest timestamp and timestamp at the one-hour bound to separate variables
   before writing the query.

   ```
   db.log.find({'t': {$exists: true}}).sort({'t': 1})

   start = new Date("2013-05-17T07:09:57Z")
   end = new Date("2013-05-17T08:09:57Z")

   db.log.count({t: {'$gte': start, '$lt': end}}) #2949
   ```

10. Using Mongo's [aggregation][mongo-aggregation] functionality, can you find 
    what the most popular link clicked is? You will need to use `$group`, 
    `$sum`, and `$sort`.

   ```
   db.log.aggregate(
            [
                {$group : {_id: '$u', count: {$sum : 1}} },
                {$sort: {count: -1}},
                {$limit: 1}

            ])
   ```

## Extra Credit

MongoDB actually has some [geospatial][mongo-geospatial] facilities (don't 
worry, PostGreSQL has [even better](http://postgis.net/) ones). Using the 
geoindices and Mongo queries, find the following:

1. All clicks within 50 miles of San Francisco
2. All clicks that came from [New England][New-England]

#### CartoDB

[CartoDB](http://cartodb.com/) happens to be one of my favorite tools for 
geospatial analysis (with built in PostGIS querying). Map the clicks across 
the globe.  Visualize clicks over time with a [torque map][torque-map].

```
// flip lat, long
db.log.find().forEach(function(doc) {
    if (doc.ll) {
        db.log.update({_id: doc._id }, {$set : { ll : [doc['ll'][1], doc['ll'][0]]}});
    }
})

// add geo index
db.log.ensureIndex({ ll : "2d"})

// query with 50 miles of SF
db.log.find({ ll: { $near: [-122.4167, 37.7833], $maxDistance: 50/3959}}).pretty()
```


