// server.js
// where your node app starts

// init project
var express = require('express');
var app = express();
var exportFunc = require('./exportFunc.js');
var MongoClient = require('mongodb').MongoClient
var url = 'mongodb://ramiel:RmZ#1357@ds157964.mlab.com:57964/rmdb';
// we've started you off with Express, 
// but feel free to use whatever libs or frameworks you'd like through `package.json`.

// http://expressjs.com/en/starter/static-files.html
app.use(express.static('public'));

// http://expressjs.com/en/starter/basic-routing.html
app.get("/", function (request, response) {
  response.sendFile(__dirname + '/views/index.html');
});

app.get("/shorturl", function (request, response) {
  response.writeHead(200, { 'Content-Type': 'application/json' });
  var longUrl = request.query.url
  var shortUrlid;
  var shortUrl;
  if(exportFunc.isURL(longUrl)){
  MongoClient.connect(url, function(err, db) {
    if (err) throw err;
   var collection = db.collection('url-shortner');
   collection.findOne({"_id": {
      "oid": "shortlastid"
  }}, function(err, result) {
        if (err) throw err;
        shortUrlid = parseInt(result.lastid)+1;
        shortUrl = {
         "_id": shortUrlid.toString(),
         "longUrl": longUrl.toString(),
        "shorturl" : exportFunc.encode(shortUrlid)
        };

   collection.insert(shortUrl, function(err, data) {
        // handle error
      if (err) throw err;
        // other operations
      });

  collection.update({"_id": {
        "oid": "shortlastid"
    }},{
      $set: {
        "lastid": shortUrlid.toString()
      }
    }, function(err, data) {
      // handle error
    if (err) throw err;
      // other operations    
    });
    let obj ={
      "originalUrl": longUrl,
      "shortUrl" : "https://ramiel-url-shortener.glitch.me/" + exportFunc.encode(shortUrlid)
      };
    response.end(JSON.stringify(obj));
    db.close();
      });
  });
  }
  else{
    let error = {
      "error": "Wrong Url Format"
    }
    response.end(JSON.stringify(error))
  };
});
app.get("/:codedUrl", function (request, response) {
  var codedurl = request.params.codedUrl;
  MongoClient.connect(url, function(err, db) {
      if (err) throw err;
    var collection = db.collection('url-shortner');
    var id = exportFunc.decode(codedurl).toString()
    collection.findOne({"_id": id}, function(err, result) {
      if (err) throw err;
      if(result){
        response.redirect(result.longUrl);
      }
      else{
        response.end("Not funded");
      }
       db.close();
       });
    });
});

// listen for requests :)
var listener = app.listen(process.env.PORT, function () {
  console.log('Your app is listening on port ' + listener.address().port);
});
