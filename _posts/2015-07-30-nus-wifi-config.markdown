---
layout: post
title: "NUS SoC Student Wifi configuration for Linux system...and some rant"
date: 2015-07-30 23:52 +0800 SGT
comments: true
summary:    Using thumbnails in your Carte Noire articles.
categories: jekyll
thumbnail: jekyll
tags:
 - thumbnails
 - carte noire
---

## The Rant

It's only few days away before my official first year in NUS! I was quite disappointed since I didn't manage to join the camp or the orientation week - I sign up 5 days before the FOW starts haha...my bad. Anyway so I decided to do my own orientation instead! The only senior I know in SoC was my classmate during secondary school. It's been so long since I met him last time, he changed a lot! A WHOLE LOT MORE happier now!

Anyway, it was lucky that I still manage to find someone that will explain to me all the CORS and bidding stuff. I really hate these tedious stuff. Not to mention that I'm also a super BAD planner. I'm always rely on friends to remind me things that I need to do. I guess I should drop this bad habit of mine and become more independent now.

I must also mention that the western at TechnoEdge (near Faculty of Engineering) is damn cheap and yummy!!! I don't mind having it everyday HAHA!!! I shall spend my rest of the time looking for fooooood before term start!

Saw a few SoC freshmen were playing nearby while I was on the way back to Hackerspace. They are having fun with FOW as I walk pass and nobody could recognize I'm also a freshman. Well can't say I'm not sad, but I guess I'll try to be more active next time then. Positivity WINS!!!

Hackerspace is great! I love the silence and the ambient. It got all the books, monitors and books...even soldering equipment! That means I can do my Raspberry Pi and Arduino project here YAY!!!

## The Main Point Is...

Now back to the main topic. I spent my day to configure NUS wifi on my Archlinux lappy. It was great that SoC did provides configuration for all platforms, but even for linux it's still suggesting we are using GUI instead. For a minimalist like me, I tend to do it in command line way if possible, if not complicated. NUS Wifi is using WPA2-ENTERPRISE with PEAP, so it's a bit tricky to setup without GUI.

Since I'm using Archlinux, the configuration might defer for other variants, but generally it applies.

The prerequisite is that one must have wpa_supplicant package installed on the system. As for Archlinux, I can choose to use systemd-netowkd that comes with the default installation instead, without the bloated GUI.

So running the command to make sure I have `wpa_supplicant` package installed:

```bash

pacman -S wpa_supplicant

```

Make sure I have either `dhclient` or `dhcpcd` package installed as well:

```bash

pacman -S dhclient dhcpcd

```

Check the wireless interface name by issuing:

```bash
ip link

```

For `systemd-networkd` users, simply try to connect to NUS network and a template should be generated for you. Otherwise, for pure `wpa_supplicant` users, you should look into `wpa_passphrase` to generate the psk and basic config section.

Create or find a configuration file under /etc/netctl/NUS:

```text
Description='NUS Student network configuration'
Interface=wlp3s0
Connection=wireless
Security=wpa-configsection
WPAConfigSection=(
        'ssid="NUS"'
        'scan_ssid=1'
        '#psk="{YOUR NUSNET PASSWORD}"'
        'psk={YOUR GENERATED PSK}'
        'key_mgmt=WPA-EAP'
        'eap=PEAP'
        'identity="NUSSTU\{YOUR NUSNET ID}"'
        'password="{YOUR NUSNET PASSWORD}"'
        'phase2="auth=MSCHAPV2"'
)
ESSID=NUS
IP=dhcp
DHCPClient="dhclient"

```

Change `DHCPClient` to `dhcpcd` instead if you are using it.

Now try to connect to the NUS wifi:

```bash
wifi-menu

```
It should be able to connect successfully now, without GUI!


## Troubleshooting

If anything fails, run `journalctl -xe` to see the logs

To see the output while wireless interface trying to associate with NUS wifi, you can run:

```bash
wpa_supplicant -i {network interface name} -c /etc/wpa_supplicant/{configuration file with wpa_supplicant format} 

```