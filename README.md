glance.js
===

[![Build Status](https://travis-ci.org/jarofghosts/glance.png?branch=master)](https://travis-ci.org/jarofghosts/glance)

Glance provides a quick disposable http server for static files

## installation

``
npm install -g glance
``

## usage

Run `glance` from within a directory and you are immediately serving the files from within that directory

If the directory being served has a `.glance.json` file within it, configuration will be read from that. Failing that, glance
will look for a `~/.glance.json` for directives. Failing **that**, glance will use defaults. Command line options will always
override config file options.

## config format

Your config should be valid json in the following format (shown with defaults):

```json
{
  "port": 61403,
  "indexing": false,
  "dir": "/whatever/dir/you/are/in",
  "verbose": false,
  "nodot": false
}
```

## as a module

Alternatively, you can `require('glance')` and use it as a module within your own code.
The glance module provides access to the Glance object as well as `createGlance`, a factory method.

Some sample code might just look something like this:

````js
var glance = require('glance'),
    http = require('http'),
// init a glance object with custom options (all totally optional)
    g = createGlance({
      dir: '../Files', // defaults to current working dir
      port: 86753, // defaults to 61403
      indexing: true, // will provide a directory list when requested, defaults to false
      nodot: true, // will hide dot files from lists and will not serve them, defaults to false
      verbose: true // defaults to false
    });

// pass requests to glance if you wanna
http.createServer(function (req, res) {
  if (/^\/static\//.test(req.url)) return g.serveRequest(req, res);
  // pretend i do other stuff here...
}).listen(5309);

// or, use it to start a static file server
g.start();

// listen for read events
g.on('read', function (req) {
  console.dir(req);
  /* req object of format:
     { fullPath: 'requested path',
       ip: 'remote ip address',
       method: 'requested method',
       response: 'response object' }
  */
});

// listen for error events
g.on('error', function (req) {
  console.log('BAD!!!!');
  // stop the glance server
  g.stop();
});
````

## command line options

`glance`
- `-d [dir]` serve a different directory than the current working directory
- `-h` print help screen with option listing
- `-i` serve a directory index when requested
- `-n` hide dot files
- `-p [port]` open server a different port than 61403
- `-V` print version information
- `-v` enable verbose mode, printing log to stdout. Off by default

## license

MIT
