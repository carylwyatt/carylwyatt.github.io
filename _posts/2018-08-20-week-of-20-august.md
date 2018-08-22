---
layout: post
title: "week of 20 august"
date: 2018-08-20
---

### 20 aug

Kicking off this school year by wiping the "new" SD card! :boom: :star2:

After finishing last week by saving a clone/image of the new SD card, I used GParted to delete all partitions and reformat the remaining bit to 32FAT. Now etcher is doing its thing and re-imaging from the saved image on the flash drive. If this works, I'd like to try it again using `dd` instead of etcher. I'm not sure why `dd` didn't work the first time I flash the SD card, but my bet is that my image wasn't a complete image. We'll find out!

It worked! Amazing! I need to tweak the image, though. There were already containers built/running, which we probably don't want to do. I will erase all images/containers, and save the image again. Then try to `dd` instead of using etcher to flash.

Tweaked the base image, saved it to the usb stick, wiped the SD card, then used dd to transfer image. It took FOREVER (like... 30 minutes?) but it worked! Etcher is much faster, but at least I can give people options. 

#### LibInsight API

I've never worked with POST data before! Exciting day for me. 

Had to do some reading, obviously. I knew that I wanted to use the [request library](https://www.npmjs.com/package/request). Then I found a [stack overflow answer about sending POST data in node.js](https://stackoverflow.com/questions/6158933/how-to-make-an-http-post-request-in-node-js) which really helped. I kept getting hung up on the HTML form because I'm not using a UI... so how could I use a form? Duh, you don't need one with node.js (backend, not front end). 

### 21 aug

The LibInsight API call is working! Made some changes to the gate-counter.js app that I'll be pushing to github tomorrow. Today was pretty busy with regular library stuff, so I didn't have a bunch of time to work on this. More tomorrow! 

### 22 aug

Made some tweaks the libinsight file. Everything seems to be working well! I'm going to have to add some math to cut the count in 2 (one for incoming, one for outgoing). Now that the libinsight issue has been worked out, what was left on the to-do list? Weird board closing problem, and bash script on startup. On to weird board closing problem!

Learned that if you use `CMD ["node"]` instead of `CMD ["npm", "start"]`, docker drops you straight into a node REPL, which might be perfect for the gate-counter app! I'm waiting until the app sends the latest data before I take it offline for hacking.

While I'm waiting, I started thinking about the bash script, which led me to thinking about how `unattended-upgrades` works. Specifically: when I get an upgrade, how do I know it happened and does it restart my programs when it installs? There is, of course, a [debian wiki articles for unattended-upgrades](https://wiki.debian.org/UnattendedUpgrades) which I should defintely check in on before writing the bash script.

#### board closing stuff

I learned:
- some docker containers use the node command instead of npm start or node xxx.js
- this worked in some containers, but because of the resin image, I need to set an entrypoint (I guess)
- see:
  - [similar resin issue with beaglebone image](https://forums.resin.io/t/beaglebone-green-node-image-hangs-on-run/2491)
  - [docker docs on setting entrypoint](https://docs.docker.com/engine/reference/run/#entrypoint-default-command-to-execute-at-runtime)

I tried `docker run -it --cap-add=ALL --privileged -v /lib/modules:/lib/modules --entrypoint /bin/bash new-test` with CMD set to "node" in the Dockerfile, and it was pretty much exactly like running `docker exec`. Pick up here when you begin again tomorrow! 
