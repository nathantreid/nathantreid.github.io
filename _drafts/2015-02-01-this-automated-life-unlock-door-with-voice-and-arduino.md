---
title: This automated life: unlocking a door with your voice and Arduino
---

This automated life: unlocking a door with your voice and Arduino
---

Home automation is another long-time interest of mine. A few months ago, I decided to automate the locking/unlocking of our apartment door. I'd been looking for a good reason to get an Arduino, and this settled it. A quick search online turned up some other peoples experiences doing this, and away I went.

Parts List:

* [Servo](https://www.sparkfun.com/products/11965)
* [CC3000 WiFi Breakout](https://www.sparkfun.com/products/12072)
* [Aduino Uno R3](https://www.sparkfun.com/products/11021)
* [Buttons](https://www.sparkfun.com/products/10302)
* [Break-away Headers](https://www.sparkfun.com/products/116)
* [Arduino Case](https://www.sparkfun.com/products/12839)
* [Servo Power Supply](https://www.amazon.com/gp/product/B00DID6C0S/ref=oh_aui_detailpage_o06_s00?ie=UTF8&psc=1)
* [DC Power Pigtail](https://www.amazon.com/gp/product/B00R1XMC6E/ref=oh_aui_detailpage_o07_s00?ie=UTF8&psc=1)

Assembly was pretty straightforward; first I got the servo up and running following the [Knob tutorial](http://arduino.cc/en/Tutorial/sweep). Then I hooked up the WiFi following SparkFun's [CC3000 hookup guide](https://learn.sparkfun.com/tutorials/cc3000-hookup-guide) and whipped up the iOS apps.

There were a few crucial things I learned along the way:

1. If your servo power supply is separate from your Arduino power supply, **be sure to connect the grounds together!** I didn't do this at first, and it caused a lot of crazy behavior (such as the Arduino resetting and the servo motor jumping around).
1. Use a power supply that allows you to use get the max torque from your servo. Initially, I ran my servo off 5V, but that wasn't enough for it to consistently lock or unlock my door. Once I got a 6V power supply, the servo performed as expected.
1. Get a pigtail or barrel jack to connect your power supply to your servo. This saves you from having to cut off a good power supply end.
1. Customize the servo control code to your servo. If you get the one above, that means changing the min and max pulse widths to 700 and 2300, respectively. If you don't, you servos will stutter when they get towards the limits of their turning capabilities.
1. Solder the CC3000 connectors on. At first, I just hooked wires through the holes (I couldn't find my soldering gun). It was a pain to keep stable though, and I wasn't sure if the issues I was experiencing were caused by faulty code or my jenky wiring.
1. Update the CC3000 pinout code to match your layout. The samples I got used a different layout, so my CC3000 didn't work at first. Here are the correct pins after following the SparkFun guide for the UNO:

```cpp
// These are the interrupt and control pins
#define ADAFRUIT_CC3000_IRQ   2  // MUST be an interrupt pin!
// These can be any two pins
#define ADAFRUIT_CC3000_VBAT  7
#define ADAFRUIT_CC3000_CS    10
```

Now, my end goal is for the door to both lock and unlock by voice command. This particular lock is unfortunately asymmetrical, which has added complexity to getting both functions working. The minimum viable product, however, was that the door unlock when I want it to (so I can unlock it coming down the hallway laden with groceries and my 15-month old daughter). Therefore I currently only have unlocking in place, but will hopefully soon have the free time to get the locking function working as well.

The HTTP server was ridiculously easy to set up due to a working example from [Adafruit](https://github.com/adafruit/Adafruit_CC3000_Library/blob/master/examples/HTTPServer/HTTPServer.ino). I just had to plug in a handler for my URL paths (/unlock and /lock) and it was ready to be attached to the door. I first tried using Instamorph to mold a "handle" around the lock servo, but it was pretty big and ugly. Then I tried wrapping string around the lock base and connecting that to the servo, as another person had done - but the servo wasn't able to generate enough torque this way. I'd like to take another look at this, as it solves the asymmetry issue, but in the meantime I have instead wrapped the string around the lock handle:

<a href="http://www.youtube.com/watch?feature=player_embedded&v=jaPH5OXVci0" target="_blank">
  <img src="http://img.youtube.com/vi/jaPH5OXVci0/0.jpg" alt="Video - Unlocking the door with Arduino" width="240" height="180" border="10" />
</a>

For unlocking the door, I whipped out a quick Cordova application that just sends a GET request to the /unlock endpoint on the Arduino. Thanks to jailbreaking, I can use "Hey, Siri" while walking around and easily launch the app with just my voice:

<a href="http://www.youtube.com/watch?feature=player_embedded&v=Dd-_AVQUlXo" target="_blank">
  <img src="http://img.youtube.com/vi/Dd-_AVQUlXo/0.jpg" alt="Video - Unlocking the door with my voice" width="240" height="180" border="10" />
</a>

I've also modified the app to talk back with either a confirmation or failure message. At this point, the app is just the sample Cordova application with tect-to-speech added and the following method called on launch:

```javascript
function execute() {
    $.get('http://192.168.2.167/unlock')
        .done(function () {
            texttospeech.speak("OK, I unlocked the door.");
        })
        .fail(function () {
            texttospeech.speak("Get it yourself!");
        });
}
```

My next step is to set up a Node.js web server with secure access, and use that to make enable unlocking via the Internet. I'll be sure to post an update detailing that when I get the chance.

The Arduino source code is on GitHub: [https://github.com/nathantreid/arduino-door-lock](https://github.com/nathantreid/arduino-door-lock).
