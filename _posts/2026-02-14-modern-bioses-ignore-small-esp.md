---
layout: post
published: true
title: "Notice: Modern PC BIOSes will ignore small EFI System Partitions (ESP)"
author: Varun
image: /img/technology.jpg
categories: technology pc
---

Small note, but important:

If you are reformatting or repartitioning disks, and using EFI/UEFI, your EFI System Partition (ESP) _must_ be probably at least 100MB or 1GB. If it's smaller than that, your BIOS will silently ignore the partition and refuse to boot.

No disk partitioning utility, boot repair utility, or bios screen warns you about this requirement. It doesn't matter if your ESP is easily large enough to fit all of the files you need to fit.

Posting this on the personal blog so hopefully it won't disappear off the internet.