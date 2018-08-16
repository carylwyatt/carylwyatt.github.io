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

After some googling, it seems the kernel panic error `unable to mount root fs on unknown block` might be due to the wrong type or amount of partitions    on the SD card. To verify this theory, I popped the original SD card into the card reader, and lo and behold, there are three partitions on it. The latest flashed card has 4. I'm going to run the flash again or try reformatting and reflashing... we'll see how it goes.

**Another duh moment**: I'm pretty sure I was using a busted image... heh. I had downloaded the latest raspbian stretch onto a usb stick. I then assumed I'd extracted it to the chromebook, but I see now that this probably did not happen. I wasn't using the ol' noggin' very well, because the usb stick I had downloaded the zip file onto is only 4GB and the extracted image of raspbian is ~4GB all on its own. I just don't think it's possible that all 4GB made it onto the stick or the chromebook. 

I re-downloaded the zip file to a larger USB stick, and I'm currently flashing the card. Instead of trying to unzip the image, I used the zip directly from etcher's file-picker. I didn't realize this was an option, but it indicated it would be flashing ~4GB of an image, which sounds right. 

Before I could flash the card, I had to reformat the SD card... again. This time I used GParted (which I kept hearing about but didn't want to install becuase I'm a stubborn idiot). It was way way easier using GParted than any of the CLI partition stuff I've been doing. 

`sudo gparted` launched the program. I picked out the device I needed to reformat, deleted all partitions except one, then reformatted it to FAT32. It wiped it all in one go, and I was ready to open etcher and move on. Let's hope this round of flashing actually works.

### success!

It's been a little over a week of trying to figure out this whole flashing an SD card situation. I knew it would take a while to figure out, but I did *not* expect it to take *this* long. I think etcher really helped, but I'd like to try again with the command line `dd` version (now that I know I had the wrong image on my USB stick) once I've gotten this whole imaging thing down.

Onto ssh-ing!

### brand new pi 

It's been about a year since I've set up a fresh install of raspbian, so I guess it's time to go back and read notes/watch screencasts of what I did before. :sweat_smile:

*Oh, man. I was such a sweet summer child in these videos. I thought I knew what I was in for, but I had no idea.*

## 16 aug

After talking with Scott last night, I decided to do some reading/youtubing about SSH. He was confused about why IT would request the pis have ssh keys. I think they're very concerned about security. My concern is whether *I* would be the one setting up/issuing keys or if *IT* wants to do the key stuff.

Instead of working on SSH, I think I'm going to change gears and work on setting up the image. I'll change the username/password for security purposes, then work on installing docker and setting up auto-upgrades. I'll need to push my docker image from yesterday up to docker hub so I'll be able to pull it down on my the new image. Once all of that is working, I'll hopefully be able to get an answer from IT about SSH. 

I also need to work on creating an image from what's installed on this new SD card. I know there are lots of tools out there for this kind of thing, I just need to find one that works on this chromebook. 

### setting up the new pi

Walked through setup screens:
- set up country, time zone, language
- changed password
- selected (temporary) wifi network
- checked for updates 
- reboot

Installed vim (can't live without it!)

Updated network settings
- see [network](https://github.com/carylwyatt/gate-counter#raspi) section on repo for details

Installed Docker
- `curl -sSL https://get.docker.com | sh`
- `docker --version` to double check it installed correctly
- `docker ps` to verify user pi doesn't have permissions
- `sudo usermod -aG docker pi` to add pi as docker user
- reboot for user permission change to take effect

Worked through 5 security tips (except ssh):
- added user `sudo adduser ivylib`
- typed new password
- selected defaults for user info
- `sudo usermod -aG sudo ivylib`
- `sudo adduser ivylib sudo`
- change configuration: uncheck 'autologin as 'pi'
- reboot
- logged in as ivylib
- deleted user pi: `sudo userdel -r pi`
- did docker permissions again: `sudo usermod -aG docker ivylib`
- `sudo apt-get update`
- `sudo apt-get install fail2ban` (bans brute-force attempts)
- open empty jail.local file: `sudo vim /etc/fail2ban/jail.local`
- add following lines:
  ```
  [ssh]
  
  enabled = true
  port = ssh
  filter = sshd
  logpath = /var/log/auth.log
  bantime = 900
  banaction = iptables-allports
  findtime = 900
  maxretry = 3
  ```

- `sudo service fail2ban restart`
- install unattended-upgrades: `sudo apt-get install unattended-upgrades`
- configure it: `sudo vim /etc/apt/apt.conf.d/50unattended-upgrades`
- uncomment the line in the section *Unattended-Upgrade::Origins-Pattern {}* that starts "origin=Debian"...
  - `"origin=Raspbian,codename=${distro_codename},label=Raspbian";`
- under that add this line:
  - `"origin=Raspberry Pi Foundation,codename=${distro_codename},label=Raspberry Pi Foundation";`
- `:wq`
- reboot

That's everything from the 5 basic security tips for raspi article (except the ssh key stuff).

### push docker image to hub

