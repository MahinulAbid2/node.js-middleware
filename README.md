# node.js-middleware
Researching about node.js middleware

<br>
<br>

* middleware is something that can access `req` and `res`.
* Then do some work and then pass the `req` and `res` object to next `middleware` or `routing`.
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

<br>
<br>

# Types of middleware
* There are two types of middleware.
1. <b>anonymous function </b>
2. <b>middle-ware with route. </b>

<br>

#### Anonymous function middleware
```javascript
const express = require('express');
const app = express();

app.use( ( req, res, next ) => {
// anonymous function middleware needs to be used inside of "app.use()".
    console.log(req.query.id);
    console.log(req.body);
    next();
});
```

<br>

#### Route middleware
* Route middleware means it `PROCESS` the `req` and `res` object with specific routing.
* After `PROCESSING`, it gives control to the next `middleware` or next final `route` which does final work.
* Routing middleware doesn't specifically need to be inside of `app.use()`.
* It can be written individually just like normal routing.
* Only <ins>difference</ins> is that it uses `next()` to give control or move to next `middleware` or `routing`. <br> <br>
```javascript
const express = require('express');
const app = express();
const multer = require('multer');
const upload = multer({ dest: 'uploads/' });



app.post("/", upload.single(), (req, res, next) => {
// this is routing middleware
    console.log(req.body.data);
    req.body.data = JSON.parse(req.body.data);
    next();
})

// Handle file upload and form data
app.post('/', (req, res) => {
    console.log(req.body.data)
    res.send('hello'); 
});
```


<br>
<br>

* ERROR LOG: I'm unable to access the `req.body`. 
* LOG: I can access the `req.query` but I can't access `req.body`.
* In search or answer I found this information.
  <br>

  #### There are 3 basic ways to send data in post.
  1. multi-part/form-data
  2. x-www-form-urlencoded
  3. raw

<br>

Now, each of the category has its own way to handle form data.

<br>
<br>

## multi-part/form-data
* `multi-part/form-data` is used when user wants to send `file-image,etc`.
* In order to handle `multi-part/form-data`, I must need a node-package called `multer`.
* I can't even access `req.body` if I don't use `multer`. I get `{}` when I try to get `req.body`.
* `multi-part/form-data` always sends data with `key-value`. We can fetch data using `keys`.
* I can access uploaded files with `req.files.keyname`.
* I can access data with `req.body.data`.


```console
npm install multer
```
* I <ins>can't handle</ins>  `multi-part/form-data` without `multer`.
* First install multer. Then configure `multer` before use.

```javascript
const multer = require('multer');  // require multer

// setup destination
const upload = multer({ dest: 'uploads/' });
/*
Explain:
- if a user tries to upload a file like image, then "multer" will take that file
- then it will search for "upload" folder and put the file there.
- If "upload" directory is not found it will be created
- And user uploaded file will be placed there.
- From there, a developer read the file using "fs" module and do database operation.
*/

app.post("/", upload.single(), (req, res, next) => {
    console.log(req.body.data);
    req.body.data = JSON.parse(req.body.data);
    next();
})

/*
- upload.single() is a function of multer.
- It handles how many files can be uploaded,
- what type of file can be uploaded
- If i don't specify it I won't be able to fetch "req.body"
- So I can say multer handler the full form-data/multi-part
*/
```

## I will research other two forms later.

<br>
<br>
<br>

## Why and when do I need a middleware?
<b> <ins> WHY?/WHEN </ins> </b> : The proper answer is yet unknown. But I'm assuming that, `PROCESSING` the req object.

<br>

#### How can I process the req object?
* For example, `req.body` has `JSON` data. And I want to parse the `JSON` to `JavaScript Object`.
```javascript

// this is a middleware.
app.post("/", upload.single(), (req, res, next) => {
    console.log(JSON.parse(req.body.data));
    next();
})
```
### Notice that this is just logging data in the console. How Can I change it so that other middleware can access and use the parsed JSON data? 

<br>

* Since there is no other way to transfer parsed data using variable.
* On the other hand, the code will be very `messy` and `unpredictable` if I use variable to pass information processed by middleware.

<br>

### There is one very good way to pass information to other `middleware` or `route`
* <ins> <b> Notice that </b> </ins> `req` and `res` are both `object` which contains `data and functions` to do databasae operation.
* Once the request is made, `req` and `res` are such object that, I can `change` it.
* <ins>This information is by far one of most unthinkable approach I've never dreamt of.</ins>
* If I parse JSON and put the parsed JS Object to `req` object then it can be accessed by the next `middleware/routeHandler`. Here is CODE EXAMPLE.
```javascript
const express = require('express');
const app = express();
const multer = require('multer');
const upload = multer({ dest: 'uploads/' });


app.post("/", upload.single(), (req, res, next) => {
    console.log(req.body.data);
    req.body.data = JSON.parse(req.body.data); // changing req object.
    next();
})

// Handle file upload and form data
app.post('/', (req, res) => {
    console.log(req.body.data) // now it can fetch the req.body.data as parsed-JSON/JavaScript-Object
    res.send('hello'); 
});

app.listen(8000);
```

