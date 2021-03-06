Akismet-api
===========

[![Build Status](https://travis-ci.org/chrisfosterelli/akismet-api.svg?branch=master)](https://travis-ci.org/chrisfosterelli/akismet-api)
[![Dependency Status](https://david-dm.org/chrisfosterelli/akismet-api.png)](https://david-dm.org/chrisfosterelli/akismet-api)

Full Nodejs bindings to the Akismet (http://akismet.com) spam detection service.

Features:
* Promise and callback support
* Supports node/iojs from 0.8 to 4.1
* Supports all Akismet API features
* Uses a modern HTTP client
* Complete test suite
* No coffeescript

_Upgrading to 2.0?_ You likely don't need to change anything, but check out the [changelog](CHANGELOG.md).

Installing
--------

```bash
npm install akismet-api
```

Creating the Client
-------------------

The blog and key values are required by Akismet.
There are a set of other avaliable default options visible in the source, but you likely will not need to change those.

```javascript
var akismet = require('akismet-api');
var client = akismet.client({
  key  : 'myKey',                   // Required!
  blog : 'http://myblog.com'        // Required!
});
```

Promises and Callbacks
----------------------

All of the function methods below support both promises and callbacks!
The returned promises use the [Bluebird](https://github.com/petkaantonov/bluebird) promise library.
The following documentation primarily uses the callback version, but to return a promise simply don't provide a callback.
Here is an example of the promise version of the `verifyKey()` function:

```javascript
client.verifyKey()
.then(function(valid) {
  if (valid) console.log('Valid key!');
  else console.log('Invalid key!');
})
.catch(function(err) {
  console.log('Check failed: ' + err.message);
}):
```

Verifying your Key
------------------

It's a good idea to verify your key before use. If your key returns as invalid, the error field will contain the debug help message returned by Akismet.

```javascript
client.verifyKey(function(err, valid) {
  if (err) console.log('Error:', err.message);
  if (valid) console.log('Valid key!');
  else console.log('Invalid key!');
});
```

Checking for Spam
-----------------

The user_ip, user_agent, and referrer are required options. All other options are optional, but will provide you with better spam detection accuracy.

```javascript
client.checkSpam({
  user_ip : '123.123.123.123',              // Required!
  user_agent : 'MyUserAgent 1.0 Webkit',    // Required!
  referrer : 'http://google.com',           // Required!
  permalink : 'http://myblog.com/myPost',
  comment_type : 'comment',
  comment_author : 'John Smith',
  comment_author_email : 'john.smith@gmail.com',
  comment_author_url : 'http://johnsblog.com',
  comment_content : 'Very nice blog! Check out mine!'
}, function(err, spam) {
  if (err) console.log ('Error!');
  if (spam) {
    console.log('OMG Spam!');
  } else {
    console.log('Totally not spam');
  }
});
```

Submitting False Negatives
--------------------------

If Akismet reports something as not-spam, but it turns out to be spam anyways, we can report this to Akismet via this API call.

```javascript
client.submitSpam({
  user_ip : '123.123.123.123',              // Required!
  user_agent : 'MyUserAgent 1.0 Webkit',    // Required!
  referrer : 'http://google.com',           // Required!
  permalink : 'http://myblog.com/myPost',
  comment_type : 'comment',
  comment_author : 'John Smith',
  comment_author_email : 'john.smith@gmail.com',
  comment_author_url : 'http://johnsblog.com',
  comment_content : 'Very nice blog! Check out mine!'
}, function(err) {
  if (!err) {
    console.log('Spam reported!');
  }
});
```

Submitting False Positives
--------------------------

If Akismet reports something as spam, but it turns out to not be spam anyways, we can report this to Akismet via this API call.

```javascript
client.submitHam({
  user_ip : '123.123.123.123',              // Required!
  user_agent : 'MyUserAgent 1.0 Webkit',    // Required!
  referrer : 'http://google.com',           // Required!
  permalink : 'http://myblog.com/myPost',
  comment_type : 'comment',
  comment_author : 'John Smith',
  comment_author_email : 'john.smith@gmail.com',
  comment_author_url : 'http://johnsblog.com',
  comment_content : 'Very nice blog! Check out mine!'
}, function(err) {
  if (!err) {
    console.log('Non-spam reported!');
  }
});
```

Testing
-------

```bash
cd node_modules/akismet-api
npm test
```

Credits
-------

Author and maintainer is [Chris Foster](https://github.com/chrisfosterelli). Development was sponsored by [MemoryLeaf Media](https://github.com/memoryleaf).

License
-------

Released under the MIT license.

See [LICENSE.txt](LICENSE.txt) for more information.
