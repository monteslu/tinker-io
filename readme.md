# Tinker-io

Tinker-io is a Firmata-compatibility IO class for writing node programs that interact with [Particle devices](http://docs.particle.io/) (formerly Spark). Tinker-io was built at [IcedDev](http://iceddev.com/)

### Caveat

This is a very simplified IO class.  It is meant to be used over the particle cloud using the default Tinker sketch that ships on the device.

For a full-featured IO class you should be using [particle-io](https://github.com/rwaldron/particle-io)

### Getting Started

In order to use the tinker-io library, you will need to use the default Tinker sketch.

The host computer (where you're running your Node.js or Web application) and the Particle do NOT have to be on the same local network.

### Blink an Led


The "Hello World" of microcontroller programming:

```js
var TinkerIO = require("tinker-io");
var board = new TinkerIO({
  token: YOUR_PARTICLE_TOKEN,
//  optional - you can use your username/password instead of token  
//  username: YOUR_PARTICLE_USERNAME_EMAIL,
//  password: YOUR_PARTICLE_PASSWORD,
  deviceName: YOUR_DEVICE_NAME
});

board.on("ready", function() {
  console.log("CONNECTED");
  this.pinMode("D7", this.MODES.OUTPUT);

  var byte = 0;

  // This will "blink" the on board led
  setInterval(function() {
    this.digitalWrite("D7", (byte ^= 1));
  }.bind(this), 500);

  this.pinMode("A1", five.Pin.ANALOG);
  this.analogRead("A1", function(voltage) {
    console.log("analog read", voltage);
  });

  this.pinMode("D1", five.Pin.INPUT);
  this.digitalRead("D1", function(value) {
    console.log('digital read', value);
  });
});
```

### Johnny-Five IO Plugin

Tinker-IO can be used as an [IO Plugin](https://github.com/rwaldron/johnny-five/wiki/IO-Plugins) for [Johnny-Five](https://github.com/rwaldron/johnny-five):

```js
var five = require("johnny-five");
var TinkerIO = require("tinker-io");
var board = new five.Board({
  io: new TinkerIO({
    token: YOUR_PARTICLE_TOKEN,
    deviceId: YOUR_PARTICLE_DEVICE_ID
  })
});

board.on("ready", function() {
  var led = new five.Led("D7");
  led.blink();

  var sensor = new five.Sensor("A0");

  // When the sensor value changes, log the value
  sensor.on("change", function() {
    console.log(this.value);
  });

});
```

### Custom Endpoints and Event Subscription

You can call custom endpoints and subscribe to event streams created via the [Particle Api](https://docs.particle.io/reference/firmware/photon/#cloud-functions) added to the Tinker sketch and still use johnny-five apis:

```js
var five = require("johnny-five");
var TinkerIO = require("tinker-io");
var boardIO = new TinkerIO({
  token: YOUR_PARTICLE_TOKEN,
//  optional - you can use your username/password instead of token  
//  username: YOUR_PARTICLE_USERNAME_EMAIL,
//  password: YOUR_PARTICLE_PASSWORD,
  deviceName: YOUR_DEVICE_NAME
});

var board = new five.Board({
  io: boardIO
});

board.on("ready", function() {

  var button = new five.Button("D4"); // button on pin 4

  button.on("down", function() {
      console.log("down");
      // call api
      boardIO.callApi("endpoint", "some_command", function(result) {
        console.log(result);
      });
    });

  boardIO.getEventStream('event_name', function(data){
    console.log(data);
  });
});
```
