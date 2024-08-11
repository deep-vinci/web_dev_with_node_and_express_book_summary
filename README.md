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

> Take a look at scaffolding utility provided by express

## Views and Layouts

a view is what gets delivered to the user. In the case of a website, that usually means HTML, though you could also deliver a PNG or a PDF, or anything that can be rendered by the client. For our purposes, we will consider views to be HTML.

static is the content that does not need to be changed on the fly like css, image files, js etc

## Using Handlebars as a template framework 
```js
var app = express();
// set up handlebars view engine
var handlebars = require('express3-handlebars')
        .create({ defaultLayout:'main' });

app.engine('handlebars', handlebars.engine);
app.set('view engine', 'handlebars');
```

## File structure for using Handlebars
```
├── app.js
└── views
    ├── 404.handlebars
    ├── 500.handlebars
    ├── about.handlebars
    ├── home.handlebars
    └── layouts
        └── main.handlebars
        └── custom.handlebars
        
```

It become tedious to rewrite all that repetitive code for every page, it creates a potential maintenance nightmare: if you want to change something on every page, you have to change all the files. Layouts free you from this, providing a common framework for all the pages on your site.

```html
views/layouts/main.handlebars:
<html>
<head>
    <title>Meadowlark Travel</title>
</head>
<body>
    {{{body}}}
</body>
</html>
```

{{{body}}}. This expression will be replaced with the HTML for each view. When we created the Handlebars in‐ stance, note we specified the default layout (defaultLayout:'main'). That means that unless you specify otherwise, this is the layout that will be used for any view.

```html
views/home.handlebars:
<h1>Welcome to Meadowlark Travel</h1>
```

## Static Files and Views

Static middleware allows you to designate one or more directories as containing static resources that are simply to be delivered to the client without any special handling. This is where you would put things like images, CSS files, and client-side JavaScript files.

```js
app.use(express.static(__dirname + '/public'));
```

every asset can be stored inside `/public` dir like images and css files etc

## Dynamic content

Views aren’t simply a complicated way to deliver static HTML (though they can certainly do that as well). The real power of views is that they can contain dynamic information.

```html
/views/about.handlebars
<h1>{{{text}}}</h1>
```
```js
app.get('/about', function(req, res){
    res.render('about', { text: "random text" });
});
```