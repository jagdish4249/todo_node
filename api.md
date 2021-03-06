# API

* Create a file **user.api.routes.js** inside **routers** folder and use require it in **app.js**
  ```
  app.use('/api/users', require('./routes/user.api.routes'));
  ```
  `/api/users` is written instead of `/`, to add `/api/users` as prefix to all the routes created inside the file

* Create **/api/users** get routes to list all user in json form
  ```
  const express = require('express');
  const app = express.Router();
  
  const User = require('../models/users');
  
  // List users
  app.get('/', (req, res) => {
    User.find({})
      .select('name')
      .then(users => {
        res.send({users: users});
      })
      .catch((err) => {
        res.statusCode = 400;
        res.send({error: {message: err.message}});
      });
  });
  
  module.exports = app;
  ```
* open routes, [http://localhost:3000/api/users](http://localhost:3000/api/users)

  ![User List](images/users_list.png)
  
  Response header has **Content-Type:application/json**
  
* Install [curl](https://github.com/curl/curl), which can be use as HTTP client

* ```
  curl -X GET http://localhost:3000/api/users
  ```
  This command provides us the list of users.

* Create `/api/users` post route to create a new user
  ```
  app.post('/', function (req, res) {
    const name = req.body.name;
    const user = new User({name: name});
  
    user.save()
      .then((savedUser) => {
        res.send(savedUser)
      })
      .catch((err) => {
        res.statusCode = 400;
        res.send({error: {message: err.message}});
      });
  });
  ```
  
  curl command:
  ```
  curl -X POST http://localhost:3000/api/users -d "name=Shyam Kumar"
  ```
  
* Update `/api/users/:userId` put route to update a user
  ```
  app.put('/:userId', function (req, res) {
    const userId = req.params.userId;
    const query = {_id: userId};
    const data = req.body;
    User.update(query, {$set: data})
      .then(() => {
        return User.findOne(query)
          .then((user) => {
            res.send(user);
          })
      })
      .catch((err) => {
        res.statusCode = 400;
        res.send({error: {message: err.message}});
      });
  });
  ```
  
  curl command:
  ```
  curl -X PUT http://localhost:3000/api/users/5a183efa2c402078e89586e6 -d "name=Shyam"
  ```
* Delete `/api/users/:userId` delete route to delete a user
  ```
  app.delete('/:userId', function (req, res) {
    const userId = req.params.userId;
    const query = {_id: userId};
    User.remove(query)
      .then(() => {
        res.send({message: 'User deleted successfully.'})
      })
      .catch((err) => {
        res.statusCode = 400;
        res.send({error: {message: err.message}});
      });
  });
  ```
  
  curl command:
  ```
  curl -X DELETE http://localhost:3000/api/users/5a183efa2c402078e89586e6
  ```  
  