---
title: Node.js
---

## <a id='intro'></a>Introduction ##

This quickstart guide is for developers who wish to build applications with the [Node.js](http://www.nodejs.org) Javascript framework and deploy to Cloud Foundry.

## <a id='prerequisites'></a>Prerequisites ##

* A Cloud Foundry account; you can sign up [here](https://my.cloudfoundry.com/signup)
* The [cf](../../managing-apps/cf) command line tool
* [Node.js](http://www.nodejs.org) installed using the matching version of Node.js on your Cloud Foundry instance
* [NPM](http://npmjs.org/) - Node Package Manager, to manage dependencies on your application

## <a id='create-your-app'></a>Create your application ##

For the purposes of this tutorial we will build a very simple application that makes use of the node.js package, [express](http://expressjs.com).

Open a shell and create a folder for your application in your desired location:

<pre class="terminal">
$ cd ~/Projects
$ mkdir hello-node
$ cd hello-node
</pre>

Create a file called "app.js" with the following contents:

```javascript
var express = require("express");
var app = express();

app.get('/', function(req, res) {
    res.send('Hello from Cloud Foundry');
});

app.listen(3000);
```

Create a file called "package.json" with the following contents:

```json
{
  "name": "hello-node",
  "version": "0.0.1",
  "dependencies": {
    "express": "*"
  },
  "engines": {
    "node": "0.8.x"
  }
}
```

This file tells node which libraries are in use (express, in this case) and what versions to use. The engines configuration can also be used to specify which version of node to use, although this is also selected using CF when deploying the application. For a more detailed explanation of package.json, take a look at the [Node.js documentation](https://npmjs.org/doc/json.html).


## <a id='autoconfiguration'></a>Autoconfiguration ##

At the moment the application is set to listen to port 3000. To be able to deploy the application unmodified a dependency on the cf-autoconfig node module has to be added to the project. Make the following two modifications, add cf-autoconfig to package.json

~~~json

{
  "name": "hello-node",
  "version": "0.0.1",
  "dependencies": {
    "express": "*",
    "cf-autoconfig": "*"
  },
  "engines": {
    "node": "0.8.x"
  }
}
~~~

and then require the library at the start of the application;

~~~javascript
require("cf-autoconfig");
var express = require("express");
var app = express();

app.get('/', function(req, res) {
    res.send('Hello from Cloud Foundry');
});

app.listen(3000);
~~~

Deploy the application as normal and the port will automatically be assigned, along with any bound data connections. If you do not wish to use auto configuration then just change the app to look for the bound port using environment variables;

~~~javascript
require("cf-autoconfig");
var express = require("express");
var app = express();

app.get('/', function(req, res) {
    res.send('Hello from Cloud Foundry');
});

app.listen(process.env.VCAP_APP_PORT || 3000);
~~~

## <a id='dependencies'></a>Dependencies, NPM and package.json ##

Install the modules declared as dependencies in package.json using NPM:

<pre class="terminal">
$ npm install
</pre>

This should create a "node_modules" folder that contains the application's dependencies.

Start the application as a local server and check its output:

<pre class="terminal">
$ node app.js
</pre>

Open a browser and navigate to http://localhost:3000, or, alternatively use `curl` in another shell:

<pre class="terminal">
$ curl http://localhost:3000
</pre>

You should see the output `Hello from Cloud Foundry`.

## <a id='deploy-your-app'></a>Deploying your application ##

With cf installed, target your desired Cloud Foundry instance and login:

<pre class="terminal">
$ cf target api.cloudfoundry.com
Setting target to https://api.cloudfoundry.com... OK

$ cf login
</pre>

Deploy the application by using the `push` command. Notice the URL "hello-node.cloudfoundry.com" was taken, so it was changed to "hello-node2.cloudfoundry.com".
All the other options were left as the default by pushing enter:

<pre class="terminal">
$ cf push

Name> hello-node
Instances> 1

1: 64M
2: 128M
3: 256M
4: 512M
5: 1G
6: 2G
7: 4G
8: 8G
9: 16G
Memory Limit> 64M

Creating hello-node... OK

1: hello-node.cloudfoundry.com
2: none
URL> hello-node.cloudfoundry.com

Updating hello-node... OK

Create services for application?> n

Bind other services to application?> n

Save configuration?> n

Uploading hello-node... OK
Starting hello-node... OK
Checking hello-node... OK
</pre>

Finally check your application has deployed correctly, navigating to the configured URL.

## <a id='next-steps'></a>Next steps - Binding a service ##

Binding and using a service with Node.js is covered [here](./services.html)
