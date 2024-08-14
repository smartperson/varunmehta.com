---
layout: post
published: true
title: "Serving My Own Email and More"
author: Varun
image: /img/technology.jpg
categories: Technology
galleries:
  mail-in-a-box-setup:
    pictures:
      - url: "backup-ui-bugs.png"
        caption: "I _did_ fill in all of these fields for the S3 backup, and it works as expected. However, the UI won't show most of the values, so it looks broken/incomplete. I have no idea why, other than it being a bug."
      - url: "mail-in-a-box-checks.png"
        caption: "The status checks screen will help you take care of everything you need to until you finally get to All OK. Trust."
---

It feels like in the middle of the corporate-influenced internet's heydey, we're seeing a re-decentralized internet's nascent emergence. And for me, I think it's time to figure out and free one of the oldest parts of the internet I still use regularly: e-mail. Thankfully, it's easier now than it's ever been.

* TOC
{:toc}

#### Why?

_Why not?_ Seriously though, my reasons are manyfold:

* **No limits.** I'm tired of the regular reminders from Google to upgrade my storage, while getting casual warnings that I might stop receiving email unless I clear up my storage or start paying them for more room in my inbox. If I run my own email, I can provide my own storage, and upgrade it however/whenever I see fit.
* **No ads.** I don't like seeing Google-inserted ads in my inbox.
* **Privacy, please.** Google/Alphabet/Microsoft/Apple already know enough about me without also having access to all of my email. The less data floating out there on the corporate-controlled internet, the happier I'll be.
* **Ownership feels good.** More philosophically, I own the domains. If I own the domain, the email should be mine, too.
* **Bonuses and network effects, abound.** Depending on the exact approach, there could be more benefits to running my own email, that would apply to other aspects of my online/tech footprint.

#### Requirements

A good approach has to satisfy a few key requirements for me:

* **Keep costs "reasonable".** For now I'm looking to pay around the same price as a storage upgrade from any of the major consumer cloud providers (Apple/Google/Microsoft). I think $5-10 a month sounds about right.
* **Occasional maintenance at most.** I want an email system that is mostly "set and forget" for its core features. I'm okay with occasionally needing to log in to change settings, upgrade, or perform other maintenance tasks -- let's say maybe every two weeks or so. A good system will notify me when maintenance is required instead of making me poke aorund and check for myself.
* **Won't get marked as spam.** I need to be able to set up all of the proofs, validations, and security so that a receiving email server (especially gmail) won't mark my email or domain as spam.
* **Reliable enough that I can use it for important email.** Servers go down and I want to be prepared for that. In particular, I want to make sure incoming emails don't get lost/bounce if my server goes offline for a little bit due to maintenance or an outage.
* **Data integrity so I don't lose what I already have.** In particular, I need some kind of automated backup system in place so I don't lose my whole inbox because of a storage failure.
* **Tinkerability, please.** Good off-the-shelf solutions are cool. I still want something with some bells and whistles and knobs to play with to keep things interesting.

#### The Approach

**tl;dr:** The core of my approach will be using [Mail in a Box](https://mailinabox.email/) on a Virtual Private Server. Set up backup storage and backup DNS, too.
[{% include image.md image_url="/img/2024/08/mail-in-a-box.png" %}](https://mailinabox.email/)

##### Services

* [Contabo Virtual Private Server (VPS)](https://contabo.com/en-us/vps/) to run the server
  * $6.85/mo.
  * US East Location
  * 4 vCPUs, 6GB RAM, 130GB NVME Storage
  * 32TB monthly outbound traffic, unlimited inbound traffic
  * I thought about running the server on one of my own computers at home that's connected 24/7. However I decided against it 
* [Contabo Object Storage](https://contabo.com/en-us/object-storage/) for offsite backup
  * $2.99/mo.
  * US Central Location
  * 250GB, S3-compatible API
  * This is enough storage to last a long time for backups, and maybe also to store other stuff if/when I need to.
* [Gandi DNS](https://www.gandi.net/en-US) for Domain Name registrar and hosting
  * $24/yr.
  * Supports Glue Nameservers
  * Supports DNSSEC (for extra email deliverability)
  * Supports Advanced DNS configuration
* Backups DNSes in case the main server goes down
  * Free
  * [afraid.org](https://freedns.afraid.org/secondary/instructions.php)
  * [nether.net](https://puck.nether.net/dns/login)
  * As long as the backup DNS provides the MX records, any incoming email will be held by the sending server and retried until my email server comes back online.

##### Software

[Mail in a Box](https://mailinabox.email/) v69b is giving us the bulk of what we need. It comes with a bunch of components already set up. Just take a look at the [architecture diagram](https://mailinabox.email/static/architecture.svg)! ![architecture diagram](https://mailinabox.email/static/architecture.svg)
* Requires minimal configuration out of the box
* Runs on top of a fresh Ubuntu install
* Sends and receives email
* Provides good webmail ([Roundcube](https://roundcube.net/))
* Serves its own DNS for all of the domains you want to serve
* Hosts static web pages on any domains
* Preconfigured [NextCloud](https://nextcloud.com/) for contacts, andcalendar
* Automatically manages backups locally or in an S3 bucket
* Uses Z-Push for push email/calendar support on iOS
* Generates activity and status emails so I know about any errors or if maintenance is required

#### Guides

I'm not going to write up a full guide on MiaB, both because a good one exists, and because as soon as I write it, it will be out of date. They have a [setup guide](https://mailinabox.email/guide.html) that you should follow. Without the issues I mentioned in "Tips & Tricks" it would have taken me only a couple of hours to finish getting my email server totally up and running.

#### Tips & Tricks

I ran into a few snags along the way. I document them here in case you're looking for these terms on your web searches.

##### Name Server Daemon (NSD) v4 won't start

If you get this error, and NSD won't start, be on the alert! Your DNS won't be served because the daemon isn't running! You'll see errors on the console or `journalctl` like:

    nsd can't bind tcp socket: Cannot assign requested address
    nsd.service: Start request repeated too quickly.

[This happened to me because IPv6 is off](https://discourse.mailinabox.email/t/nsd-issue-when-ipv6-is-disabled/10873). The Name Server Daemon (NSD) that comes with Mail in a Box requires IPv6 is working on your server, and it will entirely *fail* to launch if it can't. My VPS build came with IPv6 off. Rather than disabling IPv6 for NSD, I figured it's smarter to just enable IPv6. It's the future, after all.

Turning on IPv6 was simple. I just edited `/etc/sysctl.conf` to include the line below and restarted:

    net.ipv6.conf.all.disable_ipv6 = 0

##### UI and validation errors for cloud/S3 email backup

Cloud backups in Mail in a Box have some UI quirks when want to back up in S3. Thankfully this was well documented on the [MiaB forums](https://discourse.mailinabox.email/t/unable-to-setup-s3-backups/9906/3) by syslarper. Here are a few paraphrased notes from them. Refer to image below to see the fields I'm referring to.

* Omit the protocol (http/https) and port numbers on the S3 Host/Endpoint.
* Omit the region name.
* Only port 443 is supported.
* You need to have already created the S3 bucket.

After validating and saving your settings, most of the configuration fields will be empty when you open the backups management page. Just trust that the backup is going to work, and confirm it by checking the logs and peeking in your S3 bucket the next day :-)

{% include gallery.html gallery_id="mail-in-a-box-setup" %}

##### Follow the built-in advice

Use the status checks in Mail in a Box to see what else you need to do to get everything working properly. **Also** use [Mail-Tester](https://www.mail-tester.com/) to validate that your emails are getting sent with all the right security bits set to improve deliverability!

#### How's it going?

Having been over a week, it's so far so good! Backups are getting generated. When the server is down, incoming emails are just delayed for a few minutes while the sending server waits to retry. Everything costs me about $10/month.

#### Now What?

Now we get to have some fun! Send me an email if you want to help me test things out. Also, since MiaB is set up to serve static pages, I was able to migrate my Jekyll git repos and hooks over to the server. Which means that even this blog you're reading (right now at varunmehta.com!) is published and hosted on the very same server. I might even cancel my account with [Dreamhost](https://www.dreamhost.com), my discount shared hosting provider!

&mdash;&nbsp;Varun

_Who's Varun? I've been in Product at [Noom](https://noom.com), and I was previously founder of an HR tech startup [Disqovery](http://disqovery.com). I have worn many hats, and I like making things. I also like talking business. You can reach me at [me@varunmehta.com](mailto:me@varunmehta.com), [Mastodon](https://fosstodon.org/@smartperson), [Github](https://github.com/smartperson), and [LinkedIn](https://linkedin.com/in/varunkmehta)._
