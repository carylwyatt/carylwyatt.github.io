---
layout: post
title: "week of 6 august"
date: 2018-08-06
---
## 6 aug

I talked with Scott over the weekend about npm packages and dependencies. I'm going to try taking the `^` character out of the `package.json` file for packages I want to use old stuff with. I learned that the `^` means npm will install *newer* versions of the package if there aren't breaking changes (major update). That's not what I want. 
- removed carat from `package.json`
- ran `npm install` and everything is fine?
- uninstalled johnny-five and serialport and installed v 0.12.0 of johnny-fve
- same errors: serialport @6.0.5 no prebuilt binaries for node 8.11.3, arch=arm, platform=linux
- **big duh moment**: [support for raspi linux with arch=arm](https://www.npmjs.com/package/serialport#raspberry-pi-linux) doesn't exist
- how does johnny-five have serialport as a dependency if it doesn't work on linux ARM platforms?!
- did some more digging through issues, someone suggested having `gcc` but that didn't work
- gcc itself suggested installing `google-closure-compiler-js` since they no longer update `gcc` (four years!)
- uninstalled/reinstalled johnny-five 0.12.0, but still getting the prebuilt binaries error
- read more `node-serialport` and `raspi-io` issues, all their fixes were things I already have installed, so I'm at impasse again.
- thought maybe I got really lucky when I came across [getting a raspberry pi ready for nodebots](https://github.com/nebrius/raspi-io/wiki/Getting-a-Raspberry-Pi-ready-for-NodeBots), but I've done all this stuff.
- noticed I didn't have `node-gyp` installed (*how?*), so installed that; went through j5 uninstall-reinstall, still nothing
- saw a comment in an issue about cloning the node-serialport github repo and installing it locally; took forever but it installed successfully without the same errors I've been getting
- had a thought: could my old npm version be the issue? upgraded npm version in just one directory to test; no avail

I'm going to walk away from the serialport stuff for a while. I was hoping that if I could get a clean npm install that it would clear up whatever access issue I'm experiencing inside the Docker container, but I'm still not sure that serialport and modprobe are connected in any way. I get a weird error during npm install of serialport, but still somehow end up with serialport installed, so I'm not sure that's the error that's holding this up.

Going back to building docker images:
- still working with the new [raspbian-stretch](https://hub.docker.com/r/schachr/raspbian-stretch/) image
- it must be pretty bare bones... no node/npm installed, so I'm working on that
- sudo not installed??
- installed sudo but now it's getting stuck on `pigpio` *surprise*

I don't have time to run this whole thing again today, so tomorrow I need to:
- preinstall pigpio
- take a look at [Alpine Linux](https://github.com/node-serialport/node-serialport/tree/master/packages/serialport#alpine-linux) for serialport

## 7 aug

Starting the day off strong with a new directory for exploring a docker container with Alpine Linux/node.
- repo: [mhart/alpine-node](https://github.com/mhart/alpine-node)
- copied Dockerfile, .dockerignore, gate-counter.js, and package.json
- updated files to match new image
- attempted to build image
- got a weird error

After googling, I found that resin has an alpine-node base image specifically for rpi3. 
- docker hub: [resin/raspberrypi3-alpine-node](https://hub.docker.com/r/resin/raspberrypi3-alpine-node/)
- made new directory
- copied Dockerfile, .dockerignore, gate-counter.js, and package.json
- updated files to match new image

Alpine is ***very*** diferrent than Raspbian. Package manager is apk instead of apt-get. I'm struggling to get normal packages installed. When I try to get npm to install my packages, it says it can't find [pigpio](http://abyz.me.uk/rpi/pigpio/http://abyz.me.uk/rpi/pigpio/). That's because it's not installed. But I can't figure out how to install pigpio without apt-get! I tried downloading wget, unzip, and make so I could download the package, unzip it, and install it using make, but now I'm getting an error from make, and I DON'T UNDERSTAND ANY OF THIS.

Gotta take a break until tomorrow.

## 8 aug

I was pretty fed up yesterday. Let's hope some tunes and extra coffee can help me figure this out today.

Current situation:
- Depending on the docker build, I'm having issues with either
	- `pigpio` which needs to be installed on the system *as well as* in the node modules :fearful:
	- `raspi-io` my/Johnny Five's chosen library for gpio pins on the raspi, which has to be *installed* as non-root user but then must be *run* as root :persevere:
	- `serialport` because of a pre-built binaries problem :poop:
- Docker images/OSes
	- Raspbian was an easy choice, since it's what I built my app with
		- For whatever reason, all the raspbian builds I've done have failed because of the above libraries
		- Several `serialport` issues suggested using an alpine node build, so I switched, but that's causing a whole new set of problems

Since I can't debug alpine issues from my raspi, I built a blank `resin/raspberrypi3-alpine-node` container (no app) so I can work on installing pigpio from inside
- used the Dockerfile from the docker hub page
- ran the conatiener using `--cap-add=ALL --privileged` because it was struggling to build the resin files
- gave me some error about not having a `CMD` command (I don't)
- so I exited the container, then used `docker exec <container ID> /bin/bash` to start messing around in alpine 
- made sure all the things were installed (build-base, sudo, python)
- added the things [serialport suggests](https://github.com/node-serialport/node-serialport/tree/master/packages/serialport#alpine-linux) (linux-headers, udev)
- git cloned gate-counter from repo
- attempted to `npm install` the stuff from README
- hung up on raspi-io because `pigpio` isn't installed; can't auto install because `apt-get` isn't a thing in alpine 
- pigpio install is failing at same point as during Docker build: final step `sudo make install` at `ldconfig` step
	- after much googling, I think this might be because [ldconfig is a glibc thing](http://man7.org/linux/man-pages/man8/ldconfig.8.html) and [alpine is musl standard library](https://github.com/node-serialport/node-serialport/tree/master/packages/serialport#alpine-linux)  
	- confirmed: [thanks to this listserv](http://lists.busybox.net/pipermail/buildroot/2016-June/164150.html)
	- I believe this means alpine linux is out
	- going to step away from the alpine stuff for now

Going back to the original `new-test` directory and seeing if any of my new-found knowledge will help debug this nonsense
	- added pigpio stuff to Dockerfile's `RUN apt-get install` line and double checked my node_modules
	- still getting all that crazy error stuff when installing `serialport` but I don't think it matters
	- spun up a container and got a modprobe i2c error 
	- so I guess I'm back to "do I even *need* i2c stuff since I'm not using i2c pins?"

Got into new-test container using `exec` so I could dig around in the files
- This is the full error message from the npm install stage:
	```
	Error: Command failed: modprobe i2c-dev
	modprobe: ERROR: ../libkmod/libkmod.c:557 kmod_search_moddep() could not open moddep file '/lib/modules/4.14.34-v7+/modules.dep.bin'
	``` 
- Went looking for the `/lib/modules` folder, but it doesn't seem to exist (!!)
- Some stack overflowing suggested I install `linux-headers` (d'oh! that was suggeted in the `serialport` docs, too)
- Ran `sudo apt-get install linux-headers-\`uname -r\`` and got a new error:
	```
	E: Unable to locate package linux-headers-4.14.34-v7
	E: Couldn't find any package by regex 'linux-headers-4.14.34-v7'
	```
- After more googling about resin images w/o `lib/modules/` folder, found an issue about mounting the volume at `docker run` (which again, is something that has been suggested before)
- This ran the gate-counter.js app w/o error, but also closed the board and exited the app:
	- `docker run -it --cap-add=ALL --privileged -v /lib/modules:/lib/modules carylwyatt/new-test`
- Now... how to keep it running?
