# 1. Introducing Express

Express was created by TJ Holowaychuk, a web framework inspired by Sinatra, which is a web framework built on Ruby.

> Express underwent a fairly substantial rewrite between 2.x and 3.0, then again between 3.x and 4.0. This book will focus on version 4.0.

# 2. Getting Started with Node

- Installation guides
- terminal usage for basic file commands
- deciding which editor to use

## Simple web server in node

```js
var http = require("http");

http
  .createServer(function (req, res) {
    res.writeHead(200, { "Content-Type": "text/plain" });
    res.end("Hello world!");
  })
  .listen(3000);

console.log("Server started on localhost:3000; press Ctrl-C to terminate....");
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
var handlebars = require("express3-handlebars").create({
  defaultLayout: "main",
});

app.engine("handlebars", handlebars.engine);
app.set("view engine", "handlebars");
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
app.use(express.static(__dirname + "/public"));
```

every asset can be stored inside `/public` dir like images and css files etc

## Dynamic content

Views aren’t simply a complicated way to deliver static HTML (though they can certainly do that as well). The real power of views is that they can contain dynamic information.

```html
/views/about.handlebars
<h1>{{{text}}}</h1>
```

```js
app.get("/about", function (req, res) {
  res.render("about", { text: "random text" });
});
```

# 4. Tidying Up

Best Practices

- Version control
- npm packages
- module exports

# 5. Quality Assurance

Stupid shit

# 6. The Request and Response Objects

`https://localhost:3000/about?test=1#history`

```
http:// -> protocol
localhost -> hostname
:30 -> port name
/about -> path
?test=1 -> querystring
#history -> fragment
```

## Response Headers

Browsers will respect the Content-Type header regardless of what the URL path is. So you could serve HTML from a path of /image.jpg or an image from a path of /text.html. (There’s no legitimate reason to do this; it’s just important to understand that paths are abstract, and the browser uses Content-Type to determine how to render content.)

It is also common for response headers to contain some information about the server, indicating what type of server it is, and sometimes even details about the operating system. The downside about returning server information is that it gives hackers a starting point to compromise your site. Extremely security- conscious servers often omit this information, or even provide false information. Disabling Express’s default X-Powered-By header is easy:

```js
app.disable("x-powered-by");
```

## Request Body

In addition to the request headers, a request can have a body (just like the body of a response is the actual content that’s being returned). Normal GET requests don’t have bodies, but POST requests usually do. The most common media type for POST bodies is `application/x-www-form-urlencoded`, which is simply encoded name/value pairs separated by ampersands (essentially the same format as a querystring). If the POST needs to support file uploads, the media type is `multipart/form-data`, which is a more zomplicated format. Lastly, AJAX requests can use `application/json` for the body.

## Parameters

The term "parameters" in web requests can refer to various sources like query strings, session data, request bodies, or named routing parameters, leading to confusion when they overlap. The param method in Node.js merges all these parameters, which can create bugs when conflicting values arise. This practice, influenced by PHP's $\_REQUEST variable, is discouraged. Instead, the author advocates for using dedicated properties for each parameter type to avoid confusion.

## Boiling It Down

The chances are that you will be using a small subset of this functionality most of the time. So let’s break it down by functionality you’ll be using most frequently.

## Rendering Content

Example 6-1. Basic usage

```js
// basic usage
app.get("/about", function (req, res) {
  res.render("about");
});
```

Example 6-2. Response codes other than 200

```js
app.get("/error", function (req, res) {
  res.status(500);
  res.render("error");
});

// or on one line...
app.get("/error", function (req, res) {
  res.status(500).render("error");
});
```

Example 6-3. Passing a context to a view, including querystring, cookie, and session values

```js
app.get("/greeting", function (req, res) {
  res.render("about", {
    message: "welcome",
    style: req.query.style,
    userid: req.cookie.userid,
    username: req.session.username,
  });
});
```

Example 6-4. Rendering a view without a layout

```js
// the following layout doesn't have a layout file, so views/no-layout.handlebars
// must include all necessary HTML
app.get("/no-layout", function (req, res) {
  res.render("no-layout", { layout: null });
});
```

Example 6-5. Rendering a view with a custom layout

```js
// the layout file views/layouts/custom.handlebars will be used
app.get("/custom-layout", function (req, res) {
  res.render("custom-layout", { layout: "custom" });
});
```

Example 6-6. Rendering plaintext output

```js
app.get("/test", function (req, res) {
  res.type("text/plain");
  res.send("this is a test");
});
```

Example 6-7. Adding an error handler

```js
// this should appear AFTER all of your routes
// note that even if you don't need the "next"
// function, it must be included for Express
// to recognize this as an error handler
app.use(function (err, req, res, next) {
  console.error(err.stack);
  res.status(500).render("error");
});
```

Example 6-8. Adding a 404 handler

```js
// this should appear AFTER all of your routes
app.use(function (req, res) {
  res.status(404).render("not-found");
});
```

## Form Processing

Example 6-9. Basic form processing

```js
// body-parser middleware must be linked in
app.post("/process-contact", function (req, res) {
  console.log(
    "Received contact from " + req.body.name + " <" + req.body.email + ">"
  );
  // save to database....
  res.redirect(303, "/thank-you");
});
```

Example 6-10. More robust form processing

```js
// body-parser middleware must be linked in
app.post("/process-contact", function (req, res) {
  console.log(
    "Received contact from " + req.body.name + " <" + req.body.email + ">"
  );
  try {
    // save to database....
    return res.xhr
      ? res.render({ success: true })
      : res.redirect(303, "/thank-you");
  } catch (ex) {
    return res.xhr
      ? res.json({ error: "Database error." })
      : res.redirect(303, "/database-error");
  }
});
```
## Providing an API

WIP

# Templating with handlebars

