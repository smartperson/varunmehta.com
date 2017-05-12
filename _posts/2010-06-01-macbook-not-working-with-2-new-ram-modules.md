---
layout: post
published: true
title: "Macbook not working with 2 new RAM modules, but 1 new and 1 old works?"
author: Varun
categories: Technology
image: /img/technology.jpg
credit: Adafruit
creditlink: https://www.adafruit.com
---

*Subtitle: “Whoa, I just reprogrammed a RAM chip”*

I purchased 4 GiB of RAM for my sister and brother-in-law’s old Macbook. Did you know that Apple shipped a Macbook configuration in 2008 that included only 1 GiB of RAM? Since I upgraded them to Snow Leopard (64-bit!) and they want to use [vmware Fusion](http://www.vmware.com/products/fusion/) to run Windows 7 side-by-side, they sorely needed some more memory.

I bought two of [these](http://www.amazon.com/gp/product/B000T93UR2) RAM modules for their computer and had them shipped to my parent’s address, so that they were available when I came home for the long weekend. I put both of the RAM modules in, and was dismayed by what happened when I tried to start up; instead of the gray-on-grey Apple startup screen, I got no chime and a solid white light from the power/sleep indicator. I took one of the modules out and put an old 512 MiB modules back in, and it started up and showed 2.5 GB of memory installed.

That certainly took me by surprise. I’ve seen blinking white lights to indicate different kinds of RAM failures, but never a solid one before. I searched online and was quickly met with a description of the problem: if you put only DDR800 RAM into a Macbook that uses DDR667 it cannot negotiate the RAM to use the slower speed. Unfortunately, nothing is wrong with the RAM. Instead the motherboard/firmware are incapable of properly using the memory modules. Most people suggested buying one piece of RAM that was only capable of 667 MHz operation at most, which would force the other module to run at the lower, compatible speed. Have you tried finding 667 MHz-only RAM these days?! I ordered what I thought was the required RAM, and continued to investigate.

I managed to find [this link](http://hardforum.com/showthread.php?t=1491628) to [H]ard|Forum, where someone else ran into the same problem, but ultimately found a great solution. Use SPDTool for Windows to edit the RAM’s EEPROM and tell it that it is only capable of 667MHz. Why buy any new RAM when you can just reprogram the one you have?

I popped the module into my work computer (running Windows 7, naturally), made the specified edits, wrote the new data to the module, and put it into the Macbook alongside its virgin 800MHz cousin. We are all good to go. The Macbook now shows 4GB of available RAM. I’m writing this blog post in hopes that someone else who runs into this problem will find the solution. If you enjoyed reading it or feel like giving me more nerd cred that was entirely unintentional.

“Mission Accomplished”