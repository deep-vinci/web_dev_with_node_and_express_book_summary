# 1. Introducing Express

Express was created by TJ Holowaychuk, a web framework inspired by Sinatra, which is a web framework built on Ruby.

> Express underwent a fairly substantial rewrite between 2.x and 3.0, then again between 3.x and 4.0. This book will focus on version 4.0.

# 2. Getting Started with Node

- Installation guides 
- terminal usage for basic file commands
- deciding which editor to use

## Simple web server in node

```js
var http = require('http');

http.createServer(function(req,res){
    res.writeHead(200, { 'Content-Type': 'text/plain' });
    res.end('Hello world!');
}).listen(3000);

console.log('Server started on localhost:3000; press Ctrl-C to terminate....');
```

# 3. Saving Time with Express

In Chapter 2, you learned how to create a simple web server using only Node. In this chapter, we will recreate that server using Express.