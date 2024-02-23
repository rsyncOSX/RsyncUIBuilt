+++
author = "Thomas Evensen"
date = "2024-02-20"
title =  "FreeBSD"
tags = ["FreeBSD"]
categories = ["FreeBSD"]
lastmod = "2024-02-20"
+++
I have been using servers as backups and sharing files for a long time. I have also been very facinated [about ZFS](https://openzfs.org/wiki/Main_Page) as a filesystem and commenced using it as part of OpenSolaris. To make a long story short, the size and cost of my servers have reduced over the years. Last year, 2023, I sold my dedicated server HW and cabinet. On that server, I connected two WD Red Sata SSD discs, 500 GB each, in a mirrored setup by ZFS. The server OS was FreeBSD 14, and when the server was sold, I kept my SSDs as a backup of my data.

# Raspberry Pi4 - 8GB ram

I am not particularly interested in HW. My interests in IT are SW and development. But I have read about a Single Board Computer, aka SBC, named Raspberry Pi and I knew there was FreeBSD support for the Pi4. And the Raspberry Pi4 with 8 GB RAM is now my server setup. 

# FreeBSD 14 and Netdata

It is very easy to install FreeBSD on it; there are several guides around. My Pi4 now runs plain vanilla FreeBSD 14. The two SSDs are connected, and ZFS filesystems were imported. Netdata is downloaded from GitHub and compiled on the Pi4 for monitoring. Max power consumption is between 8 and 9 watts. 

The setup is very light and easy to bring. Every time I spend some days in my cabin, I just shut down the Pi4, disconnect and bring it, and start it up again in my cabin. The servername is bound to the MAC- address on the Pi4, and the Internet routers at home and cabin are setup to give the Pi4 the servername. And I have online backups of my data both at home and in my cabin. 

{{< figure src="/images/freebsd/raspberrypi.png" alt="" position="center" style="border-radius: 8px;" >}}
{{< figure src="/images/freebsd/netdata.png" alt="" position="center" style="border-radius: 8px;" >}}

