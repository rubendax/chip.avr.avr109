# chip.avr.avr109

Flash firmware onto an avr device running a bootloader that speaks the [avr109](http://www.atmel.com/images/doc1644.pdf) protocol.


#### This is a fork of https://github.com/tmpvar/chip.avr.avr109 which will always allow the CATERIN bootloader regardless of the user specified board signature.
##### User specified signature can be used for an alternative bootloader if needed.
```javascript
      if (d.toString() !== that.signature && d.toString() !== 'CATERIN') { // avr109 will always accept CATERIN bootloader
        fn(new Error('Invalid device signature; expecting: ' + that.signature + ' or CATERIN - received: ' + d.toString()));
      }
```

## use

```javascript
var avr109 = require('chip.avr.avr109');
var serialport = require('serialport');
var sp = new serialport.SerialPort('/dev/tty.usbmodemfd121');
var fs = require('fs');

sp.on('open', function() {
  fs.readFile(__dirname + '/test.hex', function(err, data) {

    avr109.init(sp, { signature: 'LUFACDC' }, function (err, flasher) {
      if (err) {
        throw err;
      }
      flasher.erase(function() {
        console.log('initialized');

        flasher.program(data.toString(), function(err) {
          if (err) throw err;
          console.log('programmed!');

          flasher.verify(function(err) {
            if (err) {
              throw err
            }
            flasher.fuseCheck(function(err) {
              if (err) throw err;
              console.log('OK!');
            });
          });
        });
      });
    });
  });
});
```

## license

[MIT](LICENSE.txt)
