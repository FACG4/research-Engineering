# what modules
Good authors divide their books into chapters and sections; good programmers divide their programs into modules.

Like a book chapter, modules are just clusters of words (or code, as the case may be).

Consider modules to be the same as JavaScript libraries.

A set of functions you want to include in your application.



## Why use modules?

- Maintainability
- Namespacing
- Reusability

## How can you incorporate modules?
- Module pattern
-- The Module pattern is used to mimic the concept of classes (since JavaScript doesn’t natively support classes) so that we can store both public and private methods and variables inside a single object 

1. Anonymous closure
```javascript
(function () {
  // We keep these variables private inside this closure scope

  var myGrades = [93, 95, 88, 0, 55, 91];

  var average = function() {
    var total = myGrades.reduce(function(accumulator, item) {
      return accumulator + item}, 0);

      return 'Your average grade is ' + total / myGrades.length + '.';
  }

  var failing = function(){
    var failingGrades = myGrades.filter(function(item) {
      return item < 70;});

    return 'You failed ' + failingGrades.length + ' times.';
  }

  console.log(failing());

}());
```
2. Global import
```javascript
(function (globalVariable) {

  // Keep this variables private inside this closure scope
  var privateFunction = function() {
    console.log('Shhhh, this is private!');
  }

  // Expose the below methods via the globalVariable interface while
  // hiding the implementation of the method within the
  // function() block

  globalVariable.each = function(collection, iterator) {
    if (Array.isArray(collection)) {
      for (var i = 0; i < collection.length; i++) {
        iterator(collection[i], i, collection);
      }
    } else {
      for (var key in collection) {
        iterator(collection[key], key, collection);
      }
    }
  };

  globalVariable.filter = function(collection, test) {
    var filtered = [];
    globalVariable.each(collection, function(item) {
      if (test(item)) {
        filtered.push(item);
      }
    });
    return filtered;
  };

  globalVariable.map = function(collection, iterator) {
    var mapped = [];
    globalUtils.each(collection, function(value, key, collection) {
      mapped.push(iterator(value));
    });
    return mapped;
  };

  globalVariable.reduce = function(collection, iterator, accumulator) {
    var startingValueMissing = accumulator === undefined;

    globalVariable.each(collection, function(item) {
      if(startingValueMissing) {
        accumulator = item;
        startingValueMissing = false;
      } else {
        accumulator = iterator(accumulator, item);
      }
    });

    return accumulator;

  };

 }(globalVariable));
  ```
3. Object interface
```javascript
var myGradesCalculate = (function () {

  // Keep this variable private inside this closure scope
  var myGrades = [93, 95, 88, 0, 55, 91];

  // Expose these functions via an interface while hiding
  // the implementation of the module within the function() block

  return {
    average: function() {
      var total = myGrades.reduce(function(accumulator, item) {
        return accumulator + item;
        }, 0);

      return'Your average grade is ' + total / myGrades.length + '.';
    },

    failing: function() {
      var failingGrades = myGrades.filter(function(item) {
          return item < 70;
        });

      return 'You failed ' + failingGrades.length + ' times.';
    }
  }
})();

myGradesCalculate.failing(); // 'You failed 2 times.'
myGradesCalculate.average(); // 'Your average grade is 70.33333333333333.'
```
4. Revealing module pattern
```javascript
var myGradesCalculate = (function () {

  // Keep this variable private inside this closure scope
  var myGrades = [93, 95, 88, 0, 55, 91];

  var average = function() {
    var total = myGrades.reduce(function(accumulator, item) {
      return accumulator + item;
      }, 0);

    return'Your average grade is ' + total / myGrades.length + '.';
  };

  var failing = function() {
    var failingGrades = myGrades.filter(function(item) {
        return item < 70;
      });

    return 'You failed ' + failingGrades.length + ' times.';
  };

  // Explicitly reveal public pointers to the private functions
  // that we want to reveal publicly

  return {
    average: average,
    failing: failing
  }
})();

myGradesCalculate.failing(); // 'You failed 2 times.'
myGradesCalculate.average(); // 'Your average grade is 70.33333333333333.'
```
## CommonJS and AMD

### CommonJS

```javascript
function myModule() {
  this.hello = function() {
    return 'hello!';
  }

  this.goodbye = function() {
    return 'goodbye!';
  }
}

module.exports = myModule;
```
```javascript
var myModule = require('myModule');

var myModuleInstance = new myModule();
myModuleInstance.hello(); // 'hello!'
myModuleInstance.goodbye(); // 'goodbye!'
```
## AMD -- Asynchronous Module Definition

```javascript
define(['myModule', 'myOtherModule'], function(myModule, myOtherModule) {
  console.log(myModule.hello());
});
```
## UMD
For projects that require you to support both AMD and CommonJS features, there’s yet another format: Universal Module Definition (UMD).

``` javascript

(function (root, factory) {
  if (typeof define === 'function' && define.amd) {
      // AMD
    define(['myModule', 'myOtherModule'], factory);
  } else if (typeof exports === 'object') {
      // CommonJS
    module.exports = factory(require('myModule'), require('myOtherModule'));
  } else {
    // Browser globals (Note: root is window)
    root.returnExports = factory(root.myModule, root.myOtherModule);
  }
}(this, function (myModule, myOtherModule) {
  // Methods
  function notHelloOrGoodbye(){}; // A private method
  function hello(){}; // A public method because it's returned (see below)
  function goodbye(){}; // A public method because it's returned (see below)

  // Exposed public methods
  return {
      hello: hello,
      goodbye: goodbye
  }
}));
```


# Require();
To include a module, use the require() function with the name of the module:

`var http = require('http');`
 # Exports
Use the exports keyword to make properties and methods available outside the module file.

`exports.myDateTime = function () {
    return Date();
};
`

## Asynchronous forms of functions

 ** Why should you use asynchronous forms of functions wherever possible in Node? 

 to avoid blocking the event loop. But some people take this idea too far. Asynchronous loops are necessary when there is a large number of iterations involved or when the operations within the loop are complex. But for simple tasks like iterating through a small array, there is no reason to over complicate things by using a complex recursive function.
A simple synchronous for/while loop works just fine, and will also be faster and more readable.
_________________________________________________--
What are error-first callbacks, and why is it important to follow that pattern in your own code?

The "error-first" callback has a simple rule that the first argument for the callback function should be an error object. If the error argument is null, then the operation was successful and if the error argument is not null, then an error has occurred.

Almost all Node.js code follows this style because without it developer has to spent a lot of time in maintaining different signature and styles for each and every callback.


`var callback = function(err, data) {
    if (err) { /* do something if there was an error */ }

    /* other logic here */

};`

 Why should you avoid using throw in callbacks?

The throw statement throws a user-defined exception. Execution of the current function will stop (the statements after throw won't be executed), and control will be passed to the first catch block in the call stack. If no catch block exists among caller functions, the program will terminate.

When might you use the syncronous form of a function instead?

URL Module

The URL module splits up a web address into readable parts.
To include the URL module, use the require() method:

var url = require('url')
Parse an address with the url.parse() method, and it will return a URL object with each part of the address as properties.

Example:
varurl = require('url');
varadr ='http://localhost:8080/default.htm?year=2017&month=february';
varq = url.parse(adr,true);

console.log(q);
Url {
protocol: 'http:',
slashes: true,
auth: null,
host: 'localhost:8080',
port: '8080',
hostname: 'localhost',
hash: null,
search: '?year=2017&month=february',
query: { year: '2017', month: 'february' },
pathname: '/default.htm',
path: '/default.htm?year=2017&month=february',
href: 'http://localhost:8080/default.htm?year=2017&month=february' }

console.log(q.host);//returns 'localhost:8080'
console.log(q.pathname); //returns '/default.htm'
console.log(q.search); //returns '?year=2017&month=february'

varqdata = q.query;//returns an object: { year: 2017, month: 'february' }
console.log(qdata.month); //returns 'february'

Query String Module
Definition and Usage
The Query String module provides a way of parsing the URL query string.

Syntax
The syntax for including the Query String module in your application:
var querystring = require('querystring');
Example:
ar querystring = require('querystring');
var q = querystring.parse('year=2017&month=february');
console.log(q);
{ year: '2017', month: 'february' };
console.log(q.year) //return 2017

URLObject:
This class contains properties and methods for accessing and modifying the constituent components of a URL.URLObject instances are immutable, as they derive from the built-inunicode, and therefore all methods return new objects; you need to consider this when usingURLObjectin your own code.

Example:
u = URLObject("http://www.google.com/")
console.log(u)
http://www.google.com/
URL objects feature properties for directly accessing different parts of the URL:scheme,netloc,username,password,hostname,port,path,query and fragment.
All of these have a with method for adding/replacing them, and some have a withoutmethod for removing them altogether. The query string and path also have a variety of methods for doing more fine-grained inspection and manipulation.

scheme
This URL’s scheme.

print(URLObject("http://www.google.com").scheme)
http
with_scheme(scheme)
Add or replace this URL’s scheme.
print(URLObject("http://www.google.com").with_scheme("ftp"))
ftp://www.google.com

print(URLObject("//www.google.com").with_scheme("https"))
https://www.google.com
netloc
The full network location of this URL.
This value incorporates username, password, hostname and port.
print(URLObject("http://user:pass@www.google.com").netloc)
user:pass@www.google.com
with_netloc(netloc)
Add or replace this URL’s netloc.
print(URLObject("http://www.google.com/a/b/c").with_netloc("www.amazon.com"))
http://www.amazon.com/a/b/c
username
This URL’s username, if any.
print(URLObject("http://user@www.google.com").username)
user
print(URLObject("http://www.google.com").username)
None
with_username(username)
Add or replace this URL’s username.
print(URLObject("http://user@www.google.com").with_username("user2"))
http://user2@www.google.com
without_username()
Remove this URL’s username.
print(URLObject("http://user@www.google.com/").without_username())
http://www.google.com/
password
_

Click here to type a chat message. Supports GitHub flavoured markdown
