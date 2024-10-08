---
layout: post
published: true
title: "IPv6 Learning Followups: Google and Ubiquiti (Unifi UXG) Gateways"
author: Varun
image: /img/technology.jpg
categories: Technology
galleries:
---

**The advice in this post is for more advanced computer/network users.** One month later, I've had to adjust a few things for our IPv6 setup. And rather than posting it exclusively on corporate social media, I'm writing it up here and sharing on the fediverse (Mastodon).

#### Recap

[Previously]({% link _posts/2024-08-27-ipv6-learning-and-adoption.md %}), I went through the certifications to get /64 and /48 reserved IPv6 spaces from Hurricane Electric, and set up a dual stack configuration for our whole home using HE's free [tunnelbroker.net](https://tunnelbroker.net) service.

Overall, things have been working well. I was skeptical of what speeds to expect, but given that HE runs a huge part of the internet backbone, and the tunnel entry point is local to us in New York, it's no surprise that latency and throughput are just as good as our ISP's direct IPv4 service.

#### Solving the Google IPv6 Tunnel Problem with my Pi Hole

As mentioned in the last post, Google seems to treat any IPv6 tunnel user as a malicious actor/VPN/shady person, and the whole family has been struggling with increasingly impossible CAPTCHAs if they want to run a Google web search. YouTube, Gmail, and other Google services work without issue.

They've mostly migrated over the DuckDuckGo as their default search engines, but I still wanted to provide some relief for when Google search was necessary. Thankfully, since I run a [Pi Hole](https://pi-hole.net/) locally, I can control how any domain name resolves on our home LAN. 

Set up a Pi Hole rule to specifically block IPv6 (AAAA record) DNS resolution for all *.google.com domains.

* Domain/RegEx: `(\.|^)google\.com;querytype=AAAA`
* Type: Regex blacklist
* Status: Enabled
* Comment: "No Google IPv6" or something else that will help you remember

#### Ubiquiti EdgeRouter X IPv6 Firewall Rules are Awkward
Since every IPv6 address is publicly routable, it is **vital** that you keep good firewall rules in place. We cannot rely on NAT+Port Forwarding for protection the way we do for IPv4 LANs.

I guess since the EdgeRouter X/Max series is older, it only has a GUI for creating IPv4 firewall rules. If you want IPv6 firewall rules, then you need to add them using the console or the config tree. This is okay, but I found it awkward to set up and validate that the rules were working as expected. Coupled with the fact that the aging EdgeRouter X was under high CPU load with all of our LAN traffic, and I used this as an excuse to upgrade to Ubiquiti's newest line of Unifi-compatible gateways. Specificaly the [5-port UXG Max](https://store.ui.com/us/en/products/uxg-max). It would be easier to manage it together with our Unifi wireless access point, ande it has a nice-looking and functional GUI for setting up IPv4 and IPv6 firewall rules. **However…**

#### Uniquiti Unifi UXG Gateways don't natively support IPv6 Tunnels!

My bad for assuming that a newer product would _at least_ be as capable as the older (cheaper) product, I guess. There are no GUI tools for creating a tunnel, and there is no config tree create whatever you need, either. Ubiquiti Support confirmed to me that IPv6 tunnels are not supported on the UXG series :-(

##### Add an IPv6 Tunnel to the UXG Max _anyway_

We can still SSH into the gateway, so there has to be a way. Turns out there is, and it works better than I expected, once you jump through some setup hurdles. Basically:

1. Modify and set up the UDM [on_boot.d](https://github.com/unifi-utilities/unifios-utilities/tree/main/on-boot-script-2.x) daemon, which will run whatever scripts you want on boot.
2. Add the [UDMP-ipv6 startup scripts](https://github.com/telnetdoogie/UDMP-ipv6) to on_boot.d, to set up the IPv6 tunnel on every boot.
3. Set up your IPv6 firewall rules on the Unifi GUI as `Internet v6 In` like a normal person would.
4. Add the UDMP-ipv6 cron jobs. That will keep copying Internet v6 firewall rules over to the correct interface.

###### 1. Modify and install on_boot.d on the UXG
This is the most technically complex part. Do this right and the rest is smooth sailing.

[Set up SSH console access to your UXG device](https://help.ui.com/hc/en-us/articles/204909374-UniFi-Connect-with-Debug-Tools-SSH). 

Follow the instructions in the [UDM Boot Script Readme](https://github.com/unifi-utilities/unifios-utilities/tree/main/on-boot-script-2.x) for a remote installation.

> **NOTE:** If you are using a UXG device, as of this date, the maintainer has not added support for it in the installation script. You can check out my open [pull request](https://github.com/unifi-utilities/unifios-utilities/pull/631/files) to see how to edit the script for your own device. Run `ubnt-device-info model` on your device to get the correct model string to use on [line 78](https://github.com/unifi-utilities/unifios-utilities/pull/631/files#diff-7d14b9e47ed349ffcb22db70dc9b5c704a1859ff39566bd43fa7c5366c3ad893R78). You will have to copy a modified version of the remote_install.sh script over to your UXG and run it from there.

##### 2. Add IPv6 tunnel startup scripts
Add the [UDMP-ipv6 startup scripts](https://github.com/telnetdoogie/UDMP-ipv6) to your boot scripts. Follow the instructions in that README. No modifications are necessary for UXG.

##### 3. Set up IPv6 firewall rules
Do this as normal using the Unifi GUI to set them up as `Internet v6 In` advanced rules or else simple rules. Both will work.

##### 4. Add the UDMP-ipv6 cronjobs
This is also documented in the README inside [UDMP-ipv6 startup scripts](https://github.com/telnetdoogie/UDMP-ipv6). This wil make sure that your `Internet v6 In` rules will apply to the IPv6 traffic that's coming into your tunnel.

##### What's Next
Nothing is left here! I might contribute to the community-developed [Unifi OS Utilities](https://github.com/unifi-utilities/unifios-utilities/) project to help get it working for UXG devices, too.

&mdash;&nbsp;Varun

_Who's Varun? I've been in Product at [Noom](https://noom.com), and I was previously founder of an HR tech startup [Disqovery](http://disqovery.com). I have worn many hats, and I like making things. I also like talking business. You can reach me at [me@varunmehta.com](mailto:me@varunmehta.com), [Mastodon](https://fosstodon.org/@smartperson), [Github](https://github.com/smartperson), and [LinkedIn](https://linkedin.com/in/varunkmehta)._
