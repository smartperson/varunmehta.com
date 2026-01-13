---
layout: post
published: false
title: "Running Prestashop 9 with SSL (only) using Caddy and Docker"
author: Varun
image: /img/technology.jpg
categories: technology
galleries:

---
**Prestashop is a common self-hosted ecommerce platform, but in some ways it's stuck in its state as a legacy platform; HTTPS/SSL integration is one of them.** These days every website is expected to use SSL, especially if you're using e-commerce. Prestashop makes it difficult to use modern self-hosted paradigms to do this, and expects you to use its built-in systems for configuring and serving secure content.

**To be clear, I am not a Prestashop Platform/PHP expert, just a general tech-proficient nerd. Someone more knowledgeable could find a cleaner solution here.**

#### tl;dr if you know all the basics and just need the solve

* Set up prestashop using [docker compose](https://devdocs.prestashop-project.org/9/basics/installation/environments/docker/) with production-style settings.
* Use [caddy-docker-proxy](https://github.com/lucaslorentz/caddy-docker-proxy).
* Update the labels in the prestashop docker-compose to use Caddy
* Connect to the docker mysql container and run `UPDATE ps_configuration SET value=1 WHERE name="PS_SSL_ENABLED";`
* Update the Prestashop SEO config to point to the same domain for both regular and SSL traffic

&mdash;&nbsp;Varun

_Who's Varun? I've been in Product at [CLEAR](https://clearme.com), [Noom](https://noom.com), Hotel Tech with ALICE (now [Actabl](https://actabl.com/)), and I was previously cofounder and head of product of an HR tech startup [Disqovery](http://disqovery.com). I have worn many hats, and I like making things. I also like talking business. You can reach me at [me@varunmehta.com](mailto:me@varunmehta.com), [Mastodon](https://fosstodon.org/@smartperson), [Github](https://github.com/smartperson), and [LinkedIn](https://linkedin.com/in/varunkmehta)._
