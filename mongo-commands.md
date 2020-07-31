# Mongo Commands
## Set Up
#### Start mongo server
```js
    brew services start mongodb-community@4.2
```

#### Connect to mongo shell
```js
    mongo
```

## Common Commands
#### Show all databases
```js
    show dbs
```
#### Create DB
```js
    use database
```

#### Add Collections
```js
    db.createCollection(collection)

        or

    db.collection.insert({})
```

#### List All Collections
```js
db.your_collection_name.find()
```

## Inserting
#### Insert
```js
    db.collection.insert({})
```

## Find
#### Find All for Single Field and Single Value
```js
    db.collection.find({field:"value"})
```

#### Find by ObjectID
```js
    db.collection.find({field:"value"})
```

#### Find value1 or value2
```js
    db.collection.find({field:{$in: ["value1", "value2"]}})
```

#### Find Single Value in Multiple Fields
```js
    db.collection.find({field1:"value", field2:"value"})
```

#### Find Multiple Fields with Multiple Values
```js
    db.collection.find({ field1:"value", field2:{$in : ["value1", "value2"]}})
```

#### Find value1 or value2 from multiple fields
```js
    db.collection.find({$or: [{field1:"value1"}, {field2:"value2"}]})
```

## Update
#### By ObjectId
```js
db.collection.updateOne({_id: ObjectId("5f064f36992ff89e1ecec706")}, {$set: {field:"value"}})
```
#### By One Field and Value
```js
db.collection.updateMany({field1:"value1"}, {$set:{field2:"value2"}})
```
## Delete
#### By ObjectId
```js
db.burger.remove({_id: ObjectId("5f064f36992ff89e1ecec706")})
```
#### By One Field and Value
```js
db.collection.remove({field1:"value1"})
```