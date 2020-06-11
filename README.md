# json-server


mkdir json-server-test
cd json-server-test
yarn init -y
yarn add json-server --save-dev


make a db.json

ex)
{
  "a": [
    {
      "id": 1
    },
    {
      "id": 2
      }
  ],
  "b": [
    {
      "id": 1
    },
    {
      "id": 2
    }
  ]
}


-- run 
yarn json-server --watch db.json 
// json-server --watch db.json --port 5000
// default port is 3000



-- change run script

insert - "start": "json-server --watch db.json"

{
  "name": "json-server",
  "version": "1.0.1",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "json-server --watch db.json"
  },
  "author": "h",
  "license": "ISC",
  "keywords": [],
  "description": "json test server",
  "devDependencies": {
    "json-server": "^0.16.1"
  }
}



-- custom route
yarn add lowdb

make a server.js

// server.js
const jsonServer = require('json-server');
const server = jsonServer.create();
const router = jsonServer.router('db.json');
const middlewares = jsonServer.defaults();

// using lowdb for controll db.json
const low = require('lowdb');
const FileSync = require('lowdb/adapters/FileSync');
const adapter = new FileSync('db.json');
const db = low(adapter);

// Set default middlewares (logger, static, cors and no-cache)
server.use(middlewares);

// Add custom routes before JSON Server router
server.delete('/todos/completed', (req, res) => {
  // lowdb를 사용해서 db.json에서 completed: true인 todo를 제거
  db.get('todos')
    .remove({ completed: true })
    .write();

  // todos를 응답
  res.send(db.get('todos').value());
})

// Use default router
server.use(router);

server.listen(3000, () => {
  console.log('JSON Server is running')
});
