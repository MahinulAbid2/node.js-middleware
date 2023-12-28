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
