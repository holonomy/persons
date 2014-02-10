# levelgraph-persons

store, retrieve, delete, and search [foaf:person](http://xmlns.com/foaf/spec/#term_Person)s using [levelgraph-jsonld](https://github.com/mcollina/levelgraph-jsonld)

## methods

```
var level = require('level');
var db = level('./mydb');
var persons = require('levelgraph-persons')(db, opts);
```

### persons.put(obj, function (err, obj) {

Storing persons is extremely easy:
```javascript
var manu = {
  "@id": "http://manu.sporny.org#person",
  "name": "Manu Sporny",
  "homepage": "http://manu.sporny.org/"
};

persons.put(manu, function(err, obj) {
  // do something after the obj is inserted
});
```

### persons.get(id, function (err, obj) {

Retrieving a JSON-LD object from the store requires its `'@id'`:
```javascript
persons.get(manu['@id'], function(err, obj) {
  // obj will be the very same of the manu object
});
```

### persons.del(id, function (err) {

In order to delete an object, you can just pass it's `'@id'` to the
`'@del'` method:
```javascript
persons.del(manu['@id'], function(err) {
  // do something after it is deleted!
});
```

### persons.search(constraints, function (err, solution) {

```javascript
var manu = {
  "@id": "http://manu.sporny.org#person",
  "name": "Manu Sporny",
  "homepage": "http://manu.sporny.org/",
  "knows": [{
    "@id": "https://my-profile.eu/people/deiu/card#me",
    "name": "Andrei Vlad Sambra",
    "based_near": "http://dbpedia.org/resource/Paris"
  }, {
    "@id": "http://melvincarvalho.com/#me",
    "name": "Melvin Carvalho",
    "based_near": "http://dbpedia.org/resource/Honolulu"
  }, {
    "@id": "http://bblfish.net/people/henry/card#me",
    "name": "Henry Story",
    "based_near": "http://dbpedia.org/resource/Paris"
  }, {
    "@id": "http://presbrey.mit.edu/foaf#presbrey",
    "name": "Joe Presbrey",
    "based_near": "http://dbpedia.org/resource/Cambridge"
  }]
};

var paris = 'http://dbpedia.org/resource/Paris';

persons.put(manu, function(){
  persons.search([{
    subject: manu['@id'],
    predicate: 'http://xmlns.com/foaf/0.1/knows',
    object: db.v('webid')
  }, {
    subject: db.v('webid'),
    predicate: 'http://xmlns.com/foaf/0.1/based_near',
    object: paris
  }, {
    subject: db.v('webid'),
    predicate: 'http://xmlns.com/foaf/0.1/name',
    object: db.v('name')
  }
  ], function(err, solution) {
    // solution contains
    // [{
    //   webid: 'http://bblfish.net/people/henry/card#me',
    //   name: '"Henry Story"'
    // }, {
    //   webid: 'https://my-profile.eu/people/deiu/card#me',
    //   name: '"Andrei Vlad Sambra"'
    // }]
  });
});
```

## license

MIT
