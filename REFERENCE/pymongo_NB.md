

```python

```

# Connect to a MongoDB

With the mongo client we will make a connection to a database and with that connection object make a object connected to a specific collection.


```python
from pymongo import MongoClient


# Create instance of the MongoClient class
client = MongoClient()
database = client['class_example']   # Database name (to connect to)
collections = database['users'] # Collection name (to use)
```

In this example the collections object allows us to interact with the given collection in ways that are close to how we interacted with mongo in the terminal


```python
# Search a database with a curser

res = collections.find({'name' : {'$ne' : 'Joe'}})
```


```python
# Res is now a generator and will give you one returned row at a time.
res.next()
```




    {'_id': ObjectId('5d6422b863559152178d65eb'),
     'name': 'Dan',
     'age': 13.0,
     'friends': ['Joe', 'Sam']}




```python
# As res is a generator now if we want to get back all records in a 
# list we have to type cast it as a list

res = collections.find({'name' : {'$ne' : 'Joe'}})

ret = list(res)
print(type(ret))
print(type(ret[0]))
print()
print(ret)
```

    <class 'list'>
    <class 'dict'>
    
    [{'_id': ObjectId('5d6422b863559152178d65e8'), 'name': 'Bob', 'age': 23.0, 'friends': ['Scott C', 'Ryan'], 'fav_color': 'green'}, {'_id': ObjectId('5d6422b863559152178d65e9'), 'name': 'Dan', 'age': 33.0, 'fav_color': 'red'}, {'_id': ObjectId('5d6422b863559152178d65ea'), 'name': 'Sam', 'age': 93.0, 'fav_color': 'blue'}, {'_id': ObjectId('5d6422b863559152178d65eb'), 'name': 'Dan', 'age': 13.0, 'friends': ['Joe', 'Sam']}, {'_id': ObjectId('5d6422b963559152178d65ec'), 'name': 'Sam', 'age': 13.0, 'car': 'Toyota'}]



```python
# There are built in methods into the res class like count()
res = collections.find()
res.count()
```

    /Users/danielrupp/anaconda3/lib/python3.7/site-packages/ipykernel_launcher.py:3: DeprecationWarning: count is deprecated. Use Collection.count_documents instead.
      This is separate from the ipykernel package so we can avoid doing imports until





    6




```python
# Add a record to the DB

collections.insert_one({"name" : "Sam", "friends" : [ "Scott", "Chris"], "fav_color" : "Green" })
```




    <pymongo.results.InsertOneResult at 0x10caba308>




```python
res = collections.find({'name' : {"$regex" : '.*Da.*'}})
```


```python
# Because res is a generator we can use it in a for loop
for record in res:
    print(record)
    print() 
```

    {'_id': ObjectId('5d6422b863559152178d65e9'), 'name': 'Dan', 'age': 33.0, 'fav_color': 'red'}
    
    {'_id': ObjectId('5d6422b863559152178d65eb'), 'name': 'Dan', 'age': 13.0, 'friends': ['Joe', 'Sam']}
    



```python
collections.update_many({'name' : {"$regex" : '.*Da.*'}},
                                {'$set': {'gender': 'male'} })
```




    <pymongo.results.UpdateResult at 0x10ca04288>




```python
res = collections.find({'name' : {"$regex" : '.*Da.*'}})
for record in res:
    print(record)
    print() 
```

    {'_id': ObjectId('5d6422b863559152178d65e9'), 'name': 'Dan', 'age': 33.0, 'fav_color': 'red', 'gender': 'male'}
    
    {'_id': ObjectId('5d6422b863559152178d65eb'), 'name': 'Dan', 'age': 13.0, 'friends': ['Joe', 'Sam'], 'gender': 'male'}
    


## Loading data into a Pandas DataFrame


```python
import pandas as pd
```


```python
res = collections.find({}, {'_id' : 0 })
```


```python
df = pd.DataFrame(list(res))
```


```python
df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age</th>
      <th>car</th>
      <th>fav_color</th>
      <th>friends</th>
      <th>gender</th>
      <th>name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>34.0</td>
      <td>NaN</td>
      <td>green</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Joe</td>
    </tr>
    <tr>
      <th>1</th>
      <td>23.0</td>
      <td>NaN</td>
      <td>green</td>
      <td>[Scott C, Ryan]</td>
      <td>NaN</td>
      <td>Bob</td>
    </tr>
    <tr>
      <th>2</th>
      <td>33.0</td>
      <td>NaN</td>
      <td>red</td>
      <td>NaN</td>
      <td>male</td>
      <td>Dan</td>
    </tr>
    <tr>
      <th>3</th>
      <td>93.0</td>
      <td>NaN</td>
      <td>blue</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Sam</td>
    </tr>
    <tr>
      <th>4</th>
      <td>13.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>[Joe, Sam]</td>
      <td>male</td>
      <td>Dan</td>
    </tr>
  </tbody>
</table>
</div>




```python

```
