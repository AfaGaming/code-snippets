# code-snippets

## Web Development

### Express app setup 
Here's the starting code to setup an express app:
```
const express = require("express");
const bodyParser = require("body-parser");
const ejs = require("ejs");
const mongoose = require('mongoose');

const app = express();

app.set('view engine', 'ejs');

app.use(bodyParser.urlencoded({
  extended: true
}));
app.use(express.static("public"));

// database
mongoose.connect("mongodb://127.0.0.1:27017/testDB")
.then(console.log("Connected to database."))
.catch(err => console.log("Unable to connect to database: \n" + err));

const userSchema = new mongoose.Schema({
  username: String,
  password: String
});

const User = mongoose.model('User', userSchema);

// routes

// GET

app.get("/", function(req, res) {
  res.send("Hello World");
});



// POST

app.post("/", function (req, res) {
  res.send("You posted!");
})

app.listen(3000, function() {
  console.log("Server started on port 3000");
});
```

### Authentication
Here's the code to make a basic authentication system.

Note: This is **coming soon**!
