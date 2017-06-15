---
layout: post
published: true
title: "mBot support with macOS 10.7 Lion"
author: Varun
image: /img/mbot.jpg
categories: Technology
galleries:
  mbot-ch340g:
    pictures:
      - url: "mbot-ch340g.jpg"
        caption: "an affordable, if problematic serial chip"
  mbot-problem:
    pictures:
      - url: "mbot-problem-1.png"
        caption: "The CH340G driver makes this unusually named tty device."
      - url: "mbot-problem-2.png"
        caption: "But when we try uploading to the mbot, it fails. Look at that device name though: where did the rest of it go?"
  mbot-program:
    pictures:
      - url: "mbot-program.png"
        caption: "Now that it's working, he has been *very* busy building some cool programs, all on his own."
---

The [mBot](http://www.makeblock.com/product/mbot-robot-kit) is a great little programmable robot, and my 9 year-old nephew is really getting into it. His computer can only use Mac OS 10.7.5 Lion, and the mBlock software *almost* works. Here's how I got it running perfectly.

#### tl;wr:

1. Install sketchy driver ver.1.2 for CH340G from a [Russian website](http://www.5v.ru/ch340g.htm). ([direct link](http://www.5v.ru/zip/ch341ser_mac(v12).zip))
1. Open `mBlock_v3.4.8.app/Contents/Resources/Arduino/Arduino.app/Contents/Java/hardware/arduino/avr/platform.txt`
2. Find line `tools.avrdude.upload.pattern="{cmd.path}" "-C{config.path}" {upload.verbose} -p{build.mcu} -c{upload.protocol} -P{serial.port} -b{upload.speed} -D "-Uflash:w:{build.path}/{build.project_name}.hex:i"`
3. Wrap the -P in double quotes: `tools.avrdude.upload.pattern="{cmd.path}" "-C{config.path}" {upload.verbose} -p{build.mcu} -c{upload.protocol} "-P{serial.port}" -b{upload.speed} -D "-Uflash:w:{build.path}/{build.project_name}.hex:i"`
5. Comment on my [GitHub PR](https://github.com/arduino/Arduino/pull/6383) which fixes this issue.

#### mBot

{% include image.md image_url="/img/mbot.jpg" %}

I was lucky to pick up an [mBot](http://www.makeblock.com/product/mbot-robot-kit) from my local Radio Shack at a discount before it closed. My nephew has been getting more interested in computers, robots, and programming, and I figured it would be a great gift. It uses an app called mBlock, which is based on Scratch 2.0. It has a host of interesting components: ultrasonic, line follower, IR receiver (with remote), IR transmitter, light, buttons, beeper, bluetooth, and USB/serial. It has expandable ports with RJ25 connectors. Each port provides power, i2c, and two GPIO or analog pins. This makes custom add-ons relatively easy; certainly much easier than Dash & Dot from Wonder Workshop!

#### Old computer problems

The [website](http://www.mblock.cc/download/) for mBlock simply says "latest OSX recommended." The only computer my nephew has unfettered access to runs MacOS 10.7 Lion, which was released in 2011. I was happy to find that mBlock launched fine and appeared to work. I only ran into problems once I wanted it to connect to the mBot over USB.

{% include gallery.html gallery_id="mbot-ch340g" %}

Programming the mBot requires you install an 'Arduino Driver'. Unfortunately even after installing the driver, the mBot is not recognized. The mBot and some other budget Arduino-compatible devices uses very cheap CH340/CH341 USB->Serial converters instead of the higher-quality FTDI chips. It turns out the driver included with mBlock.app only works on **MacOS X 10.9 and above**.

Rather than trying to get [MacPostFactor](http://www.osxhackers.net/macpostfactor.html) running on his computer (a MacBook 4,1), I was determined to find a driver for this chip that would work on older hardware. USB->Serial converters have been around for ages, there *has* to be a compatible driver out there, right?

Fortunately there is; however, it does feel sketchy. There is a Russian hobbyist electronics website that has a [page devoted to the CH340G](http://www.5v.ru/ch340g.htm), and includes an older version of the same driver, 1.2. This one installs fine on MacOS 10.7. **Disclaimer:** I'm not responsible for this driver messing up you're computer. I wish that was enough to get everything working, but unfortunately it isn't so simple.

#### New serial problems

{% include gallery.html gallery_id="mbot-problem" %}

As you see above, the driver doesn't work for the most frustrating reasons. The .kext creates serial devices at paths like `/dev/tty.wch ch341 USB=>RS232 1d10`, and the mBlock toolchain *doesn't escape the special characters*. This results in avrdude ignoring the entire device path after the first space. It tries to upload to an arduino at `/dev/tty.wch` and fails, with a file not found error.

#### Failed ideas

**First**, I made a symbolic link at `/dev/tty.mbot` that points to `/dev/tty.wch ch341 USB=>RS232 1d10`. The command doesn't survive restarts, so I created a [LaunchDaemon](https://gist.github.com/smartperson/d4c143525234befca0953adcf4c24983) and [shell script](https://gist.github.com/smartperson/739a0ea863cd965fddd87f263bf034ba). They have to run at startup as root, which is annoying, and means you have to secure them properly. Also, sometimes the mBot gets created with a slightly different name (ends with `1a20` instead of `1d10`), which breaks everything until you reinsert the cable and pray it works. I don't want a 9 year-old to deal with that.

**Second**, I assumed this must be a problem with mBlock's code. I went through the ([laborious](https://github.com/Makeblock-official/mBlock/issues/46), [2](http://forum.makeblock.com/t/compiling-giyhub-3-4-5-release-no-serial-ports/6987)) process of getting mBlock 3.4.5 to compile on my Mac. I changed the relevant [UploaderEx.as](https://github.com/Makeblock-official/mBlock/blob/b7522d3fc76a8cf27485e5dbe93cba20b05eb15c/src/extensions/UploaderEx.as#L52) code to wrap the device's name in double quotes. I recompiled and updated the .app package, but it still failed. avrdude seemed to be interpreting the double quotes as parts of the path. By the way, if you want to compile mBlock 3.x for Mac and you stumble on this post, you have to use AIR SDK + Compilers 19.0. You're welcome.

**Finally**, I tested this behavior on the latest vanilla Arduino app and my personal computer, and found that **this is a bug in Arduino**. It turns out this has even been [discussed before](https://github.com/arduino/Arduino/issues/3693). The post there helped me find the right Arduino txt file to fix the problem.

#### Super serial solutions

mBlock.app includes a full copy of Arduino.app, version 1.6.5-r5. You can modify the file at `mBlock_v3.4.8.app/Contents/Resources/Arduino/Arduino.app/Contents/Java/hardware/arduino/avr/platform.txt` to wrap the [`upload.pattern`](https://github.com/arduino/Arduino/blob/8d955c8be12731a4df7064e04606399c2cdad03b/hardware/arduino/avr/platform.txt#L105)'s `-P{serial.port}` option in double quotes: `"-P{serial.port}"`.

#### Finally

{% include gallery.html gallery_id="mbot-program" %}

I can relax knowing that when I'm back in New York, my nephew and his father can use his fancy new robot reliably and with ease. Now he can focus on making amazing programs, educational failures, and mischief.

I'd like Arduino to make this change in their code. I've submitted a [pull request](https://github.com/arduino/Arduino/pull/6383), but given the previous comments, it won't get approved without enough [support](https://github.com/arduino/Arduino/issues/3693).

*-vkm*

##### Who's Varun?

_I most recently was the founder of an HR tech startup, [Disqovery](http://disqovery.com). I have worn many hats, and I like making things. I also like talking business. You can reach me at [smartperson@gmail.com](mailto:smartperson@gmail.com), [@smartperson](https://twitter.com/smartperson), [Github](https://github.com/smartperson), and [LinkedIn](https://linkedin.com/in/varunkmehta)._