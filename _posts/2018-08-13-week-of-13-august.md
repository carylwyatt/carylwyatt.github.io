---
layout: post
title: "week of 13 august"
date: 2018-08-13
---

## 13 aug

Over the weekend, I took the SD card/flash drive with raspbian home to see if I could flash the card on my macbook. What I learned:
- Right off the bat, the macbook wouldn't even recognize that there was an SD card in the machine. Couldn't see it in the finder window and when I used `df -h` in the terminal, it didn't show up. I thought maybe it was just my old macbook being old (even though it flashed an SD card in the past), but I tried Scott's laptop and had the same problem.
- I think I messed up the SD card pretty badly. After the macbook stuff, I popped it into my chromebook and ran `gnome-disks`, where I learned I had 4 (!!) partitions on that thing, somehow. I watched some youtubes about how to deletes partitions and reformat SD cards, which I did.
- At the end of reformatting, I forgot to remount the device to the computer, and now my chromebook is acting like it doesn't exist.
- When I try to `mount` it via terminal, it tells me I don't have permissions. After googling, it looks like it's a polkit error, which means my whole Sunday rabbit hole only led to the end of Friday's rabbit hole.
- I guess I need to figure out this polkit/authentication nonsense. *heavy sigh*

I've been working on this for the better part of today, and I'm still not sure what it's all about. I have a few polkit(s) installed... I think? 
- Looked under `/usr/lib` for anything called polkit. Installed `policykit-1-gnome` and `policykit-1` was already installed... I think?
- looked at the policies themselves (I think??) under `/usr/share/polkit-1/actions`
- attempted to write a policy for etcher but kept running into permissions issues
- gonna have to remember how to do `chmod` read/write abilities... (so many things to remember/check on, it's very exhausting)
- doing a lot of reading about polkit/D-Bus/dameons/permissions/sudoers... but I'm trying to stick to the instructions in etcher's issues because that's really all I need to do!
- the most frustrating part is that I can see I already have an authentication agent going on start up (Applications > Settings > Settings Manager > Session and startup > Application autostart): PolicyKit Authentication Agent
  - when you hover, it says `Command: /usr/lib/policykit-1-gnome/polkit-gnome-authentication-agent-1` *so... isn't there already one running on the system? does this mean I just need to figure out how to add a policy/action for etcher to the manager???* 

Tomorrow:
- finish changing permissions on /actions etcher policy
- write new policy file
- move etcher from `/home/carylwyatt/` to `/usr/bin/` for easy execution

## 14 aug

Books & Bagels event today! It went so well!! Then we had a librarian meeting, so it's 4pm now and I probably won't get much done. I'm going to at least change the permissions on the actions folder.

**OMG** all I did was move etcher to /usr/bin and rename it etcher instead of etcher.AppImage, then removed the `org.xfce.skexec.etcher.policy` file from the `polkit-1/actions` directory and it FLASHED THE SD CARD. So excited right now, y'all.

## 15 aug

First thing this morning, I rolled out the TV and got the raspi hooked up to the screen so I could pop in the SD card and zip on along with this ssh/auto-upgrade stuff... but I got the same kernel panic error as I did last time!

I'm going to try reformatting and flashing an older kernel version. I'm pretty sure the version I have currently in production is 4.14.3- and this one is 4.14.50, so maybe that's the problem. Or maybe I bricked the SD card with some nonsense I've been trying lately. Who's to say? I do have the SD card Jonathan sent me, but I 'd rather not accidentally brick his card, too, so I'll keep on keeping on for now.
