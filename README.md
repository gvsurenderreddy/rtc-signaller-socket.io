# rtc-signaller-socket.io

This is a signaller that can be used as a drop-in replacement for
[`rtc-signaller`](https://github.com/rtc-io/rtc-signaller), that
works with a [`socket.io`](http://socket.io) server.


[![NPM](https://nodei.co/npm/rtc-signaller-socket.io.png)](https://nodei.co/npm/rtc-signaller-socket.io/)

[![unstable](https://img.shields.io/badge/stability-unstable-yellowgreen.svg)](https://github.com/dominictarr/stability#unstable) [![Build Status](https://img.shields.io/travis/rtc-io/rtc-signaller-socket.io.svg?branch=master)](https://travis-ci.org/rtc-io/rtc-signaller-socket.io) [![bitHound Score](https://www.bithound.io/github/rtc-io/rtc-signaller-socket.io/badges/score.svg)](https://www.bithound.io/github/rtc-io/rtc-signaller-socket.io) 

## Example Usage

The following examples show how a client and server can be
configured to work with socket.io, using
[`rtc-quickconnect`](https://github.com/rtc-io/rtc-quickconnect) on
the frontend.

### Server

Run using `node examples/server.js`:

```js
var http = require('http');
var server = http.createServer();
var io = require('socket.io')(server);
var board = require('rtc-switch')();
var port = process.env.PORT || 3000;

io.on('connection', function(socket){
  var peer = board.connect();

  socket.on('rtc-signal', peer.process);
  peer.on('data', function(data) {
    socket.emit('rtc-signal', data);
  });
});

server.listen(port, function(err) {
  if (err) {
    return console.error('could not start server: ', err);
  }

  console.log('server running @ http://localhost:' + port);
});

```

### Client

Run using `beefy examples/client.js`:

```js
var socket = require('socket.io-client')('http://localhost:3000');
var quickconnect = require('rtc-quickconnect');
var signaller = require('rtc-signaller-socket.io')(socket);
var freeice = require('freeice');
var qc = quickconnect(signaller, {
  room: 'socketio-signalling-demo',
  iceServers: freeice()
});

// let quickconnect know we want a datachannel
qc.createDataChannel('test');

// once the data channel is opened, let us know
qc.on('channel:opened:test', function(id, dc) {
  console.log('data channel opened with peer: ' + id);
});

```

## License(s)

### Apache 2.0

Copyright 2015 National ICT Australia Limited (NICTA)

   Licensed under the Apache License, Version 2.0 (the "License");
   you may not use this file except in compliance with the License.
   You may obtain a copy of the License at

     http://www.apache.org/licenses/LICENSE-2.0

   Unless required by applicable law or agreed to in writing, software
   distributed under the License is distributed on an "AS IS" BASIS,
   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
   See the License for the specific language governing permissions and
   limitations under the License.
