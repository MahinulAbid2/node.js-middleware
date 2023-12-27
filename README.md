# node.js-middleware
Researching about node.js middleware

<br>
<br>

* middleware is something that can access `req` and `res`.
```javascript
const express = require('express');
const app = express();

app.use((req, res, next) => {
    console.log(req.body)
    next(); // this says, the work is done, go to the next middleware
});

// Handle file upload and form data
app.post('/', (req, res) => {
    res.send('hello'); 
});

app.listen(8000);
```
* When a request is made, first--- it goes to `middleware` and then it goes to `app.post()`.
* The middleware can access the req and res object.
* ERROR LOG: I'm unable to access the `req.body`. 
* LOG: I can access the `req.query` but I can't access `req.body`.

  <br>
  <br>

  #### There are 3 basic ways to send data in post.
  1. multi-part/form-data
  2. x-www-form-urlencoded
  3. raw

<br>

Now, each of the category has its own way to handle form data.
