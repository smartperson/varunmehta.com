---
layout: post
published: true
title: "The Casually Smart Home (Part 2)"
author: Varun
image: /img/am2322.jpg
credit: Adafruit
creditlink: https://www.adafruit.com
galleries:
  am2322-analyzer:
    pictures:
      - url: "am2322-logic-hookup.jpg"
        caption: "I hooked up my logic analyzer for a little in-situ (in-kitchen) data capture."
      - url: "am2322-working-overview.png"
        caption: "This is what the conversation between Pi and AM2322 looks like when everything is working."
      - url: "am2322-working-request.png"
        caption: "There's a specific format to use when sending the request "
      - url: "am2322-working-result.png"
        caption: "Fun to analyze the results you get manually to verify things make sense."
      - url: "am2322-broken.png"
        caption: "Trying to use normal i2c libraries adds an extra write operation that sometimes mucks things up."
  am2322-connection:
    pictures:
    - url: "am2322-connection.png"
      caption: "A simplified diagram, that might be easier to understand"
  dslogic:
    pictures:
    - url: "dslogic.jpg"
      caption: I'm attached to my logic analyzer, but you probably shouldn't get it.
  galleryPreview:
    pictures:
      - url: "part2-preview.jpg"
        caption: "This little cutie is pretty powerful, but you have to know how to talk to it."
---

_Fun with sensors. I had to pick a sensor for my first working location monitors. Why was it a good choice? How much work to get it running?_

{% include gallery.html gallery_id="galleryPreview" %}

This is the part two in a series covering the project I'm working on in my spare time. My hope is to inspire some of you to try something new; maybe even using some of my mistakes to make it easier for yourselves:

* Part 1: [Overview and quick preview]({% post_url 2017-05-15-RPi-casually-smart-home-part-1 %})
* Part 2: Fun with sensors ← _(you are here)_
* Part 3: Displays
* Part 4: Casual networking
* Part 5: Bonus features
* Part 6: Physical constraints

#### Why use AM2322?

There are many weather sensors out there, but my list was quickly pared down by the following criteria:

* **It uses i2c communication.** i2c lets me use just two wires (data and clock) to interface devices with the Raspberry Pi. It usually needs no level conversion (3.3V - 5V), and it allows multiple devices to share one set of connections. Raspberry Pi has built-in support for i2c, so no need for add-ons like an analog-digital converter (like for [TMP36](https://www.adafruit.com/product/165)). Perhaps most importantly, i2c allows the Raspberry Pi to set the clock for communication. Devices like Arduino can reliably work in real time, so they can handle one-wire interfaces where the timing requirements are tight ([AM2302](https://www.adafruit.com/product/393)). Raspberry Pi has too many layers to guarantee microsecond-level timing: operating system, other processes, threading, and the python runtime. There are [RPi libraries](https://github.com/adafruit/Adafruit_Python_DHT) for the AM2302, but they do not work reliably.
* **It senses temperature *and* humidity.** My parents are aging, and Mom is particularly sensitive to humidity. It's important to us to collect that information. That rules out some other options: [MCP9808](https://www.adafruit.com/product/1782),  [MPL3115A2](https://www.adafruit.com/product/1893).
* **It's cheap (affordable).** I want to be able to make more units at a low cost. AM2322 costs about $3, which beats out most other options. High price rules out a few other choices: [HTU21D-F](https://www.adafruit.com/product/1899), [DHT22](https://www.adafruit.com/product/385), 
* **It's mountable.** Ultimately I want to stick these units into nice cases. A sensor on a breakout board would be hard to put in a case that provides enough airflow to give me good data. Sensors that come in little plastic cages seem easier to slot into a case opening. Units like the [Si7021](https://www.adafruit.com/product/3251) are no good for this reason.

*Note:* The AM2321 appears identical in function to the AM2322, but I couldn't find it available as cheaply.

#### How do we communicate with it?

The sensor has four pins: VCC, Data, Ground, and Clock. It also supports both one-wire mode and i2c mode. One-wire mode is clockless, so only VCC, Data, and Ground are used. We want i2c mode, which requires us to power the sensor (via VCC) only **after** the Clock pin is set high.

{% include gallery.html gallery_id="am2322-connection" %}

If we use the standard 3.3V or 5V power connections, the Raspberry Pi will power the sensor _before_ the i2c subsysem is initialized, which will put the AM2322 into one-wire mode. That's why we'll have to power the sensor via a RPi GPIO pin. The [AM2322.py](https://github.com/smartperson/rpi-location-monitor/blob/master/src/AM2322.py) library I modified takes care of that for us. The python code turns on the AM2322 long after the i2c subsystem starts up.

Getting data from the sensor follows this basic pattern:

1. Ping the AM2322 be attempting to read. It will fail, but it will wake up.
2. Send the actual request of the data we want. Usually we want all 4 bytes for temperature and humidity.
3. Read 8 bytes, 2 bytes the describe our request, 4 bytes of sensor data, and 2 bytes of a checksum.

This should be easy right? We can just use standard Python i2c [readList()](https://github.com/adafruit/Adafruit_Python_GPIO/blob/master/Adafruit_GPIO/I2C.py#L131) commands to get our data, since it's all according protocol. *Right?* Unfortunately it's not that simple, because…

#### the AM2322 is i2c(-ish)

I had to pore over the [datasheet](http://www.electrodragon.com/w/File:AM2322_datasheet.pdf) (similar to [AM2321](http://akizukidenshi.com/download/ds/aosong/AM2321_e.pdf)), look at example code, and finally pull out my logic analyzer to understand the issue. Other people had mentioned that the AM232* sensors aren't exactly i2c compliant, but never dived into the details of why.

{% include gallery.html gallery_id="am2322-analyzer" %}

It boils down to two basic issues:

1. When sending our request, we send the register (0x03), a starting addess (0x00), and a number of bytes (0x04). Most i2c devices do not require a request with this much information &mdash; only a register.
2. When using standard read commands like readList(), we must provide a register and length. The readList() command will actually first **write the register** out to the device. AM2322 does not expect this; it expects us to read **8 bytes raw** once we've sent our request.

The standard i2c python libraries don't provide support for this. If we try using those standard methods, it works sometimes, but it fails intermittently. For now I've written up my AM2322.py to use the rpigpio module and C code, which let's us do exactly what we want. It's not ideal, but it works, and I can find a better option later.

#### Useful code

I'm trying to keep everything I do fully updated on [GitHub](https://github.com/smartperson/rpi-location-monitor). As I mentioned before, I'll probably release a version of my [AM2322.py](https://github.com/smartperson/rpi-location-monitor/blob/master/src/AM2322.py) as a module once I get it cleaned up. It handles request throttling, pseudo-asynchronous operation, and properly powering up the AM2322 to ensure we're always in i2c mode.

In accordance with smart principles, my resin.io [Dockerfile](https://github.com/smartperson/rpi-location-monitor/blob/master/Dockerfile) and [requirements.txt](https://github.com/smartperson/rpi-location-monitor/blob/master/requirements.txt) should show you how I get the required components installed and i2c running (at a lower bitrate for stability).

#### A note on logic analyzers

Unless you want to try using new components and run into debugging issues, you probably don't need a logic analyzer. I use a [DSLogic](http://www.dreamsourcelab.com/dslogic.html), for which I have 16-channel logic analyzer and 2-channel oscilloscope attachments. It works well for what it is, but the developers don't actively maintain the software, which is based on [sigrok](http://sigrok.org). Fortunately the open source community has been submitting patches, including yours truly.

{% include gallery.html gallery_id="dslogic" %}

If you're looking for a good logic analyzer with amazing software, you have to get a [Saleae](https://www.saleae.com) - I know of no better product.

#### What's next

As always, you can keep an eye on my code work on [github](https://github.com/smartperson/rpi-location-monitor). I hope to have a post on i2c displays and casual graphics ready soon.

*-vkm*

##### Who's Varun?

_I most recently was the founder of an HR tech startup, [Disqovery](http://disqovery.com). I have worn many hats, and I like making things. I also like talking business. You can reach me at [smartperson@gmail.com](mailto:smartperson@gmail.com), [@smartperson](https://twitter.com/smartperson), [Github](https://github.com/smartperson), and [LinkedIn](https://linkedin.com/in/varunkmehta)._