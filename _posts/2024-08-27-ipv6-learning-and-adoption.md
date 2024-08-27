---
layout: post
published: true
title: "Getting Caught Up On IPv6"
author: Varun
image: /img/technology.jpg
categories: Technology
galleries:
---

A couple of things came up recently that got me thinking about IPv6 and how little I understood beyond the absolute basics. I figured that now is as good a time as any to learn and see what I can do to get involved. Here's what I accomplished and what I still have to do.

#### Why now?

_[IPv6 was drafted 1998, and ratified by the IETF in 2017.](https://en.wikipedia.org/wiki/IPv6) Why am I looking at this now?_ A few things came up around the same time that brought IPv6 back to the forefront for me:

* **Home automation.** I got a new [smart home fan](https://us.govee.com/products/goveelife-42-smart-tower-fan-2-max) which supports [Matter](https://en.wikipedia.org/wiki/Matter_(standard)), because of course I'm a nerd. The fan comes with a note that in order to control it using a Matter platform (like Apple Home), it **the fan must be connected to the network over IPv6**. I've never encountered this requirement for any device before.
* **My email server supports IPv6.** As I wrote recently, I've set up my own [personal email server](2024-08-12-serving-my-own-email-and-more.html) using [Mail-in-a-Box](https://mailinabox.email). During the setup process I noticed that Contabo (the VPS host) and the full Mail-in-a-Box stack support and provide IPv6 connectivity. When resolving the server's issues it got me to set up all of the DNS records, reverse DNS records, etc. for both the IPv4 address *and* the IPv6 address. That's pretty cool.

#### An Opportunity, a Roadblock, and an Opportunity

To learn more about IPv6, the legendary Hurricane Electric runs a fun and free [IPv6 certificate program](https://ipv6.he.net/). It's a mixture of practical tests and quizzes that get progressively more difficult for each of the 7 levels, starting at being an IPv6 Newbie all the way up to being an IPv6 Sage. There are video tutorials and online resources to help you learn what you need to know to pass each level.

Unfortunately, I hit an obstacle while getting up and running with IPv6. Early in the certificate program you need to have both a server that runs on IPv6 and _also_ a desktop that can reach IPv6 locations. I had the server covered thanks to my Contabo VPS. However, the desktop was a challenge. You see, Spectrum, my ISP for my NYC apartment, supports IPv6, but our ISP for our Long Island home, Altice/Optimum, _does not_, and has no definite plans to support IPv6 any time soon.

Thankfully, Hurricane Electric also offers a [free IPv6 tunnel service](https://tunnelbroker.net/) where you can get your own /64 block of routable IPv6 addresses for free. That's **18 pentillion** usable, public IPv6 addresses, just for me :-)

#### Tunnel Notes

Getting the tunnel set up on my Ubiquiti EdgeMAX was easy enough once I noticed they had specific instructions to follow to get it operational. They have example configurations for many common routers and operating systems. And a little bit of poking around on the internet and you're bound to find someone who has your particular configuration. After all, they now provide [56,000 IPv4 to 6 tunnels](https://tunnelbroker.net/usage/tunnels_by_country.php)!

##### Certification Success!

![](//ipv6.he.net/certification/create_badge.php?pass_name=varunmehta&amp;badge=2)

Once the tunnel was set up, most of the certificate course was easy enough to progress through. There were a few tricky questions I had to look up, and a few specific steps to take to get my IPv6 desktop and server environments set up and totally ship-shape. The learning was fun, and some of the questions/snark in the certificate course was fun, too.

##### What's Next

**Free t-shirt!** Apparently Hurricane Electric sends a nerdy-looking t-shirt to folks who finish their course to the Sage level. I didn't know this until I finished the certificate program. I look forward to receiving it and showing it off.

**Unexpected IP issues with major servers, to be resolved.** Unfortunately, now that our family home is on IPv6, it's triggering some security protections on websites like Google. While I don't mind much, as I've been minimizing how much I use Google (including Gmail), the rest of the family has noticed and is being irked by the fact they need to solve a Google CAPTCHA a couple of times a day for each device they use. Some HE tunnel users [also noticed this issue](https://forums.he.net/index.php?topic=4253.0), and I have to get caught up on the discussion and figure out if any of these will help us. It might be as simple as just waiting for Google to realize that we're legit, I don't know.

&mdash;&nbsp;Varun

_Who's Varun? I've been in Product at [Noom](https://noom.com), and I was previously founder of an HR tech startup [Disqovery](http://disqovery.com). I have worn many hats, and I like making things. I also like talking business. You can reach me at [me@varunmehta.com](mailto:me@varunmehta.com), [Mastodon](https://fosstodon.org/@smartperson), [Github](https://github.com/smartperson), and [LinkedIn](https://linkedin.com/in/varunkmehta)._
