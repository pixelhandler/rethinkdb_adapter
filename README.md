## RethinkDB Adapter

Adapter/connector for RethinkDB with simple query/persistence interface

See the [adapter.js](adapter.js) file which represents the interface for
connecting, querying, performing CRUD operations, and includes methods:

* find
* findMany
* findQuery
* createRecord
* updateRecord
* deleteRecord
* patchRecord
* findBySlug
* updateRecordBySlug
* deleteRecordBySlug
* uuid
* setup

The above method names may look familiar if you've used the Ember.js
client-side framework together with a data persistence library such as
Ember Data, or Orbit.js.

This module was developed for a simple API for a blog application
without a model layer, simply adapting a request to a resource in the
database. The adapter provides a simple interface to persist JSON data 
from a (smart) client application that has a model layer, it's own
validations, and relelationship management; such as using the JSON API
spec or JSON Patch.
 

## Install

    npm install rethinkdb_adapter --save


## Connect

Require the module

    var db = require('rethinkdb_adapter');

Environment variables used to connect to your database:

* RDB_HOST
* RDB_PORT
* RDB_DB

Below is the default connection setup

```javascript
/**
  RethinkDB database settings.
  Defaults can be overridden using environment variables.
  @method find
**/
var adapter = new db.Adapter({
  host: process.env.RDB_HOST || 'localhost',
  port: parseInt(process.env.RDB_PORT) || 28015,
  db: process.env.RDB_DB
});
```

### Examples

Below are a few examples taken from a simple blog API using Node.js w/ Express

Refer to: https://github.com/pixelhandler/blog/tree/master/server

*Create a record*, e.g. a blog post

```javascript
/**
  Create a post

  Route: (verb) POST /posts
  @async
**/
app.post('/posts', restrict, function (req, res) {
  db.createRecord('posts', req.body.posts, function (err, payload) {
    if (err) {
      logerror(err);
      res.status(500).end();
    } else {
      loginfo('payload', payload.posts);
      if (app._io) {
        loginfo('didAdd', payload);
        app._io.emit('didAdd', payload);
      }
      res.status(201).send(payload);
    }
  });
});
```

*A JSON Patch request* to update a blog post title:

```javascript
/**
  Patch a post by id, supports replace and remove operations

  Route: (verb) PATCH /posts/:id

  Example payload:

  [{
    "op": "replace",
    "path": "/title",
    "value": "Refreshed my Blog with Express and Ember.js"
  }]

  @async
**/
app.patch('/posts/:id', restrict, function (req, res) {
  db.patchRecord('posts', req.params.id, req.body, function (err) {
    if (err) {
      logerror(err);
      res.status(500).end();
    } else {
      res.status(204).end();
    }
  });
});
```

*A PUT request* to update an author

```javascript
/**
  Update a author by id

  Route: (verb) PUT /authors/:id
  @async
**/
app.put('/authors/:id', restrict, function (req, res) {
  db.updateRecord('authors', req.params.id, req.body.authors, function (err) {
    if (err) {
      logerror(err);
      res.status(500).end();
    } else {
      res.status(204).end();
    }
  });
});
```

## Links

* [RethinkDB]
* [driver]

[RethinkDB]: http://www.rethinkdb.com
[driver]: http://www.rethinkdb.com/api/javascript/
