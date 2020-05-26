---
layout: post
published: true
title: "FPGA Game Graphics Part 0: Motivation"
author: Varun
image: /img/spartan-edge-accelerator.jpg
categories: Technology, FPGA
galleries:
  replacement-connections:
    pictures:
      - url: "z680-replacement-wiring.jpg"
        caption: "The control pod (left) is wired up to the Arduino's SPI pins(middle). My replacement LCD (right) is connected to other GPIO on the Arduino."
      - url: "z680-oled-connections.png"
        caption: "A simplified diagram that might make more sense. Everything is powered from the control pod, with the Arduino acting as a data intermediary."
---
FPGA hardware has been getting a lot more affordable, and I have a few ideas for projects. The new low-cost boards that combine FPGA-style programmable logic with a fast processor are especially interesting. But before I can get to the more advanced stuff, I need to re-learn the basics. I need a plan.

* TOC
{:toc}

#### Why?

_Why not?_ My day jobs keeps me far away from low-level technology, and that's fine with me. However, as a hobby, it's an awful lot of fun. I find I learn best by picking a goal and learning as I go along to make it happen.

#### Goals

I actually just want to make a clock. The thing is, I like making strange clocks. Some examples:
* An Arduino PWM-based clock that uses analog VU-style meters to show hours/minutes/seconds.
* A Raspberry Pi running QEMU, emulating a PPC running Mac OS 9. The Mac OS 9 desktop is displayed on an e-ink display. The Mac OS desktop naturally displays the time in the menu bar. However, within the virtual machine, Internet Explorer 5 auto-refreshes a webpage hosted by the Raspberry Pi -- a simple webpage that displays current and upcoming weather conditions. _This might be worth a post of its own one day._

My strange clock game has been ramping up. People are building some [tetris-style clocks](https://hackaday.com/2019/06/13/a-tetris-clock/) which are pretty cool, and that got me thinking about making my own video game-styled clock. But I'm a visual nerd, and smooth graphics are important to me. There are plenty of interesting systems out there like the Odroid Go that even include a display, buttons, and a battery, but pretty much all of these systems have limitations that I don't like. A big one is the quality of the visuals: these devices include Serial/SPI based displays, and SoCs like the ESP32 don't include the kind of graphics hardware necessary to provide smooth (60 frames per second) motion at resolutions higher than 240x160.

{% include image.md image_url="/img/2020/05/odroid-go.gif" %}

#### Hardware

I also like playing in the boundaries between software and hardware, so taking a system like the Odroid Go and building my own graphics capabilities is exciting. As luck would have it, the folks at Seeed Studio released an "Arduino Shield" that they call the [Spartan Edge Accelerator](https://wiki.seeedstudio.com/Spartan-Edge-Accelerator-Board/). Seeed releases many dev kits, boards, and peripherals, but this one caught my attention.

{% include image.md image_url="/img/spartan-edge-accelerator.jpg" %}

At a high level, it's a board meant to be attached to an Arduino, or potentially to run standalone. It includes:

* Xilinx Spartan 7 xc7s15 (12.8k logic cells, 360 kilobits block ram)
* Espressif ESP32 "for wireless"
* HDMI port (raw)
* CSI/MIPI interface (raw)
* MicroSD slot
* ADC chip
* Accelerometer and gyroscope
* A few LEDs and push buttons

#### Requirements

Hard requirements:
* No additional processors or PCBs
* Teach myself verilog
* 480p video support
* 60fps video
* Single screen-style NES background
* NES-level sprite support
* 16-bit color

Stretch Requirements:
* 720p video support
* Super Mario Bros 3-level background scrolling
* Super Mario World-level layer support
* SNES-level sprite support
* 24-bit "true" color

#### Challenges

A few challenges already stand out to me on making any of this possible.

* **Inter-IC Communication:** The ESP32 and FPGA on the Spartan Edge Accelerator weren't really meant to continuously talk to each other: they're both supposed to communicate with an Arduino. In fact, the ESP32 is only supposed to be using i2c most of the time! To have the type of dynamic display that I want, all data updates need to be copied from the ESP32 to the FPGA during the VBLANK period. This is similar to how systems like the NES worked, where there is a CPU and separate PPU (Picture Processing Unit) and the systems do not share memory. Looking at the schematic, my best bet is likely the QSPI connection that the ESP32 can use to flash the bitstream to the FPGA upon startup. Even with that, I'll need to find a way for the FPGA/GPU to signal to the ESP32/CPU that VBLANK has begun and data can now be copied from CPU memory into GPU memory. And then there's also the challenge of just _how much_ data can be copied during the VBLANK period over a connection like that.
* **Memory Size:** The ESP32 will have plenty of RAM for our purposes, at least 512KB. However, the FPGA has no discrete memory chip, and so will depend on its block RAM, which is limited to 45KB. This is _much more_ memory than available to the NES, but it's not quite up to the **64 KB** that the SNES could take advantage of. I don't yet know how far I'll be able to get towards 16-bit graphics, especially given that we're targeting much higher screen resolutions than the NES or SNES.

#### Follow along!

This should be a fun time! I'll add more posts as I work out the rest of the systems. If you want livestreaming updates, you can check out my [YouTube Channel](https://www.youtube.com/channel/UC0V--Ek0C3I9ztkxQN6iQnw/), where I livestream most of my work on this project. Also, I'll be checking in all of my progress on [GitHub](https://github.com/smartperson/spartan-edge-accelerator-graphical-system).

&mdash;&nbsp;Varun

_Who's Varun? I work in Product at [ALICE](https://alice-app.com), and I was previously founder of an HR tech startup [Disqovery](http://disqovery.com). I have worn many hats, and I like making things. I also like talking business. You can reach me at [smartperson@gmail.com](mailto:smartperson@gmail.com), [@smartperson](https://twitter.com/smartperson), [Github](https://github.com/smartperson), and [LinkedIn](https://linkedin.com/in/varunkmehta)._
