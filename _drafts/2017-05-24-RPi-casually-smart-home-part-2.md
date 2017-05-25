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
        caption: "Trying to use normal i2c libraries adds an extra write operation that mucks things up."
  galleryPreview:
    pictures:
      - url: "part1-preview2.jpg"
        caption: "My first prototype uses an older Raspberry Pi and bodged wires. Custom dupont cables and connectors make it much cleaner."
remember: {% post_url 2017-05-24-RPi-casually-smart-home-part-2 %}
---

_Fun with sensors. I had to pick a sensor for my first working location monitors. Why was it a good choice? How much work to get it running?_

{% include gallery.html gallery_id="galleryPreview" %}

This is the part two in a series covering the project I'm working on in my spare time. My hope is to inspire some of you to try something new; maybe even using some of my mistakes to make it easier for yourselves:

* Part 1: [Overview and quick preview]({% post_url 2017-05-15-RPi-casually-smart-home-part-1 %})
* Part 2: Fun with sensors ← (you are here)
* Part 3: Displays
* Part 4: Casual networking
* Part 5: Bonus features
* Part 6: Physical constraints

#### Why use AM2322?

I want to know what's happening in other parts of my home without walking around the whole place to check. Last year we built a beautiful detached sunroom in our backyard. Mosquitoes make sitting outside in the summer difficult without some kind of physical barrier. Even though the room has windows on a sunny summer afternoon it can get too hot to use. Conversely, a cloudy autumn evening is too cold. We never know if it's a good time to head to the sunroom without checking the room ourselves, and we'd like to use it as much as possible.

There are plenty of other applications I'd like to explore: garage doors, doorbells, indoor conditions, remote monitoring, learning, teaching.

#### How do we communicate with it?

You can call it lazy, if you want, but when I build technology for my home, there are some principles I try to stick to:

1. **Easy to update.** Don't worry about firmware updates or plugging in to reconfigure and put new code on my devices.
2. **Roll with the punches.** Don't fail, crash, and lock up until a manual restart. Figure out a way to keep functioning, and report the error.
3. **Dynamic configuration.** Figure out as much about the environment as possible without manual entry. If a new device appears, add it. If an old device disappears, remove it.
4. **No apps required.** Information at a glance. Don't make me use a app to find something out.

#### It's i2c-ish

{% include gallery.html gallery_id="am2322-analyzer" %}

**MUST**

1. Stick to casual smart home principles.
2. Show current conditions.
3. Keep data on the LAN. Security matters.
4. Be (very) affordable to build and expand.

**SHOULD/MAY**

1. Allow reporting over the internet.
2. Show historical conditions.
3. Have nice graphics.
4. Allow checking conditions at a distance.
5. Produce sounds as appropriate.

#### Useful code

I was originally planning to use a bunch of ESP8266 boards, but then the Raspberry Pi Zero W went on sale for $10, and my local Micro Center has plenty in stock.

* [Raspberry Pi Zero W](https://www.raspberrypi.org/products/pi-zero-w/) $10
* Way too many [1.25mm 4-pin connectors](http://s.click.aliexpress.com/e/MjIqZbu) ($6 for 50)
* [AM2322 temperature/humidity sensors](http://s.click.aliexpress.com/e/UbqVvFa) ($3)
* 4GB MicroSD cards ($4-8)
* 128x64 I2C OLED displays ([Adafruit](https://www.adafruit.com/product/326), [Aliexpress](https://www.aliexpress.com/item/1pcs-0-96-white-0-96-inch-OLED-module-New-128X64-OLED-LCD-LED-Display-Module/32639731302.html?spm=2114.01010208.3.17.s4e55g&ws_ab_test=searchweb0_0,searchweb201602_3_10152_10065_10151_10130_10068_436_10136_10157_10137_10060_10138_10155_10062_10156_10154_10056_10055_10054_10059_100032_100033_100031_10099_10103_10102_10096_10147_10052_10053_10050_10107_10142_10051_10084_10083_10080_10082_10081_10178_10110_10111_10112_10113_10114_10181_10037_10183_10182_10185_10032_10078_10079_10077_10073_10123,searchweb201603_16,ppcSwitch_5&btsid=91a0af65-9ccd-4f7c-b0da-96561142c9e8&algo_expid=041cb1c0-512a-4ca7-aee6-fcaa828ee718-2&algo_pvid=041cb1c0-512a-4ca7-aee6-fcaa828ee718)) ($3-20)

Optional, but recommended:

* Headers to solder onto the Pi, ideally double-row right-angle connectors
* [DIY Dupont connector kit](https://www.amazon.com/gp/product/B01G0I0ZZK/ref=as_li_tl?ie=UTF8&camp=1789&creative=9325&creativeASIN=B01G0I0ZZK&linkCode=as2&tag=varmeh-20&linkId=07190cf2c194064640c8751404b41989)
* Appropriate power supplies (I got some [5V@15A](https://www.aliexpress.com/item/5v-15a-switching-power-supply-ac-dc-adapter-5v15a-5v10a-5v12a-transformer-adapter/32213159343.html?spm=2114.13010608.0.0.KNmYMn), you'll see why later) and [connectors](https://www.aliexpress.com/item/MYLB-10-Pcs-CCTV-Cameras-2-1mm-x-5-5mm-Female-Male-DC-Power-Plug-Adapter/32734002576.html?spm=2114.13010608.0.0.KNmYMn) ($5-$20)

#### A note on logic analyzers

The hardware is pretty boring for now, but it gets the job done. We use the Inter-Integrated Circuit (I2C) protocol, since with just two wires (data and clock) we can send and receive data from our temperature sensor and our display. It's pretty fast, easy to understand, and compatible across different voltages (3.3V, 5V) without conversion. The sensor uses a tight spacing for the pins (1.25mm), so those 4-pin connectors come in handy. The sensor is powered from output pin 7 on the Pi - which we'll discuss in a future blog post.

The display is beautifully bright OLED screen. We'll cover it in more details in a future blog post, as well.

{% include gallery.html gallery_id="gallery2" %}

The software is much more interesting. It's pretty much all python.

1. The Pi periodically reads the temperature and humidity.
2. Every minute it updates the OLED display with the latest information for itself and up to 3 other Pis.
3. The Pi joins a multicast UDP address.
4. It broadcasts a small packet which includes its name, conditions, timestamp, and 32x32 icon. Images and graphics will be covered in a future blog post.
5. It receives multicasts from other Pis and updates its list of known devices.
6. If it hasn't heard from another Pi in an hour, the Pi automatically removes it from the list.

#### What's next

You can keep an eye on my code work on [github](https://github.com/smartperson/rpi-location-monitor). There are some pieces around the AM2322 sensor that I'll probably release separately so other can use it easily. I need to look into OpenHab and AdafruitIO, which I have provisional support for right now.

There's a lot more to talk about what's already done, and then we can get to other fun things: sounds, sensors, lights, cases. Stay tuned!

{% include image.md image_url="/img/2017/05/part1-preview.jpg" %}

*-vkm*

##### Who's Varun?

_I most recently was the founder of an HR tech startup, [Disqovery](http://disqovery.com). I have worn many hats, and I like making things. I also like talking business. You can reach me at [smartperson@gmail.com](mailto:smartperson@gmail.com), [@smartperson](https://twitter.com/smartperson), [Github](https://github.com/smartperson), and [LinkedIn](https://linkedin.com/in/varunkmehta)._