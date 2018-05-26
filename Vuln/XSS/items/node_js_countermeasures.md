# Node.JS XSS Countermeasures
``` Node.js
var express    = require('express');
var bodyParser = require('body-parser');
var validator  = require('express-validator');

var app = express();

app.use(bodyParser.urlencoded());

app.use(validator());
app.use(function(req, res, next) {
  for (var item in req.body) {
    req.sanitize(item).escape();
  }
  next();
});

app.listen(80);
```
