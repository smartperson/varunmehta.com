---
layout: post
published: true
title: "How to repair a broken Force Sensitive Resistor (FSR) in a GalaxiPad DDR Dance Pad"
author: Varun
image: /img/technology.jpg
categories: technology pc
galleries:
  galaxi:
    pictures:
      - url: "1-pins.jpg"
        caption: "pins the FSR comes with"
      - url: "2-socket.jpg"
        caption: "IC socket fits nicely onto the pins"
      - url: "3-solder.jpg"
        caption: "solder wires to the pin"
      - url: "4-assembled.jpg"
        caption: "tape it all together (later used hot glue)"
---

I still enjoy Dance Dance Revolution (DDR), but (good) hardware is still hard to find. Ordered a [Glaxi Dance Pad](https://www.galaxidance.com/). Unfortunately, it broke after a few days of use -- and the guy that runs the company has not been responsive. One of the arrows stopped responding at all.

There was no obvious disconnect with the wiring inside. On a recommendation, I purchased [this replacement](https://buyinterlinkelectronics.com/collections/x-ux-force-sensors/products/fsr-ux-408-300mm-length) FSR strip, placed it between the aluminum bar and rubber strip, and then had to figure out how to securely connect the metal pins to the rest of the wiring for the system.

{% include gallery.html gallery_id="galaxi" %}

It originally seems like they soldered the wires directly to the FSR pins, and covered the whole thing in hot glue. Instead, I found that a double wipe 0.1" (2.54mm) IC socket fits the pins from this strip perfectly. I soldered my own wires to the pins on the socket, plugged the sensor pins into the socket, and then covered the whole thing in tape. Ultimately the tape didn't hold, and to make it a lasting repair I still covered the whole thing in hot glue. It's been working great since.