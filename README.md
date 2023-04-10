# code-snippets

## Web Development

### Express app setup 
Here's the starting code to setup an express app:
```
require("dotenv").config();
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
<br> <br>
Install command: 
```
npm i dotenv express ejs body-parser mongoose
```

### Authentication
Here's the code to make a basic authentication system.

```
const session = require('express-session');
const passport = require("passport");
const passportLocalMongoose = require("passport-local-mongoose");

// make app use express-session
app.use(session({
    secret: process.env.SECRET,
    resave: false,
    saveUninitialized: false
}));


// initilise passport
app.use(passport.initialize());
// tell app to use passport for sessions
app.use(passport.session());

// adds passportLocalMongoose (local strategy for working with mongoose) as a plugin to the schema
userSchema.plugin(passportLocalMongoose);

passport.use(User.createStrategy());

// serilize user
passport.serializeUser(function (user, cb) {
  process.nextTick(function () {
    cb(null, { id: user.id, username: user.username });
  });
});

// deserilize user
passport.deserializeUser(function (user, cb) {
  process.nextTick(function () {
    return cb(null, user);
  });
});











// routes 

// GET

app.get("/", function (req, res) {
  res.render("home");
});

app.get("/register", function (req, res) {
  res.render("register");
});

app.get("/login", function (req, res) {
  res.render("login");
});

app.get("/auth/success", function (req, res) {
  if (req.isAuthenticated()) {
    res.render("success");
  } else {
    res.redirect("/login");
  }
});

app.get("/logout", function (req, res) {
    req.logout(function (err) {
        if (err) {
            console.log(err);
            res.redirect("/logout/error");
        } else {
            res.redirect("/");
        }
    })
});

app.get("/logout/error", function (req, res) {
    res.render("error");
})

// POST

app.post("/register", function (req, res) {
  User.register(
    { username: req.body.username },
    req.body.password,
    (err, user) => {
      if (err) {
        console.log(err);
        res.redirect("/register");
      } else {
        passport.authenticate("local")(req, res, function () {
          res.redirect("/auth/success");
        });
      }
    }
  );
});

app.post("/login", function (req, res) {
    // this will get an instance of the user with the details they entered, NOT save them into the db.
    const user = new User({
        username: req.body.username,
        password: req.body.password
    });

    req.login(user, function (err) {
        if (err) {
            console.log(err);
            res.redirect("/login");
        } else {
            passport.authenticate("local")(req, res, function () {
                res.redirect("/auth/success");
            })
        }
    })
});

```

Install Command: 
```
npm i passport passport-local passport-local-mongoose express-session
```
