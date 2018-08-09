---                                                                                                                                                     
layout: post
title: "raspi/docker links"
date: 2018-08-06
---

At the beginning of this project, I had waayyyy too many tabs open. I hated reopening the same windows for weeks, so I put them here instead.

## repos/npm packages
- [Raspberry Pi and Crossbar.io IoT Starterkit Cookbook](https://github.com/crossbario/iotcookbook/tree/master/device/pi)
- [Node-gyp: Node.js native addon build tool](https://github.com/nodejs/node-gyp)
- [resin-rpi-py-ADC](https://github.com/resin-io-playground/resin-rpi-py-ADC)
- [pi-pins](https://www.npmjs.com/package/pi-pins)
	- [recommended by resin for raspi/docker GPIO use...](https://docs.resin.io/learn/develop/hardware/gpio/) don't really want to go this route since I'm already using johnny-five/raspi-io
- [node-i2c: native bindings for i2c-dev](https://github.com/kelly/node-i2c)
	- i2c-dev is holding up my app build; I don't even use the i2c pins on the rpi; can I get rid of this module altogether?
- [i2c-nodejs](https://github.com/resin-io-playground/i2c-nodejs/blob/master/Dockerfile.template)
- [rpi-kernel: build a linux kernel for raspi](https://github.com/hypriot/rpi-kernel)
- [hypriot/flash: flash SD card image of any kind](https://github.com/hypriot/flash)
- [docker hub: rpi-i2cdisplay](https://hub.docker.com/r/nicosingh/rpi-i2cdisplay/)
- [dockerfile-node-gpio-express](https://github.com/githubato/dockerfile-node-gpio-express)
	- possibility if I can get the gate-counter to work without johnny-five
- [nebrius/raspi-io](https://github.com/nebrius/raspi-io)
	- the library that is currently giving me issues
	- their [gitter channel](https://gitter.im/nebrius/raspi-io?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
- [pi-gen: create raspbian images](https://github.com/jonasrosland/pi-gen)
- [raspbian-live-build: raspi disk image builder](https://github.com/simonpoole1/raspbian-live-build)

## stack overflows/gists
- [How do you install something that needs restart in a Dockerfile?](https://stackoverflow.com/questions/39712359/how-do-you-install-something-that-needs-restart-in-a-dockerfile)
- [Using Docker with nodejs with node-gyp dependencies](https://stackoverflow.com/questions/44371864/using-docker-with-nodejs-with-node-gyp-dependencies)
- [Within a docker build, Modules dependant on node-gyp fail](https://github.com/nodejs/node-gyp/issues/1105)
- [Running a docker container as a non-root user](https://gist.github.com/alkrauss48/2dd9f9d84ed6ebff9240ccfa49a80662)
- ["Could not open moddep file ..." when mounting using a loop](https://askubuntu.com/questions/459296/could-not-open-moddep-file-lib-modules-3-xx-generic-modules-dep-bin-when-mo)
- [could not open moddep file modules.dep.bin](https://github.com/kubernetes/kubernetes/issues/55043)
- [How do I configure modprobe to find my module?](https://stackoverflow.com/questions/225845/how-do-i-configure-modprobe-to-find-my-module)
- [Streaming Video modprobe ERROR](https://raspberrypi.stackexchange.com/questions/67220/streaming-video-modprobe-error)
- ["could not insert i2c_dev" error with resinOS and Raspberry Pi 2](https://forums.resin.io/t/could-not-insert-i2c-dev-error-with-resinos-and-raspberry-pi-2/581)
	- specific johnny-five problem with resin library
- [Disable i2c in Raspberry pi](https://raspberrypi.stackexchange.com/questions/14259/disable-i2c-in-raspberry-pi)
- [RaspberryPi.org forums: I2C not working](https://www.raspberrypi.org/forums/viewtopic.php?t=51298)
- [nebrius/raspi: ERROR: could not insert 'i2c_dev'](https://github.com/nebrius/raspi/issues/2)
- [Enable i2c on ubuntu mate raspberry pi 3](https://raspberrypi.stackexchange.com/questions/61905/enable-i2c-on-ubuntu-mate-raspberry-pi-3)
- [Lsmod and modprobe not found](https://forums.docker.com/t/lsmod-and-modprobe-not-found/17558)
- [Docker loading kernel modules](https://stackoverflow.com/questions/33013539/docker-loading-kernel-modules)
	- GPIO access issues were also resolved for some users by giving docker container access to the kernel of the machine running it using the `--priveleged --cap-add=ALL` flags
- [Access GPIO pins without root. No access to /dev/mem. Try running as root!](https://raspberrypi.stackexchange.com/questions/40105/access-gpio-pins-without-root-no-access-to-dev-mem-try-running-as-root)
- [nebrius/raspi-io: ENOENT: no such file or directory](https://github.com/nebrius/raspi-io/issues/58)
- [nebrius/raspi-io: Troubles when using hypriot images and docker (johnny-five)](https://github.com/nebrius/raspi-io/issues/27)

## blogs/docs
- [nodejs.org: Dockerizing a Node.js web app](https://nodejs.org/en/docs/guides/nodejs-docker-webapp/)
- [nodejs/docker-node: Docker and Node.js Best Practices](https://github.com/nodejs/docker-node/blob/master/docs/BestPractices.md)
- [The easy way to set up Docker on Raspberry Pi](https://medium.freecodecamp.org/the-easy-way-to-set-up-docker-on-a-raspberry-pi-7d24ced073ef)
- [How to debug and fix common docker issues](https://www.digitalocean.com/community/tutorials/how-to-debug-and-fix-common-docker-issues)
- [Control GPIO with Docker Swarm](https://blog.alexellis.io/gpio-on-swarm/)
- [Create a Docker container for raspberry pi to blink an LED](https://iotbytes.wordpress.com/create-your-first-docker-container-for-raspberry-pi-to-blink-an-led/)
	- uses python, so might not be very helpful, but builds from docker's official `architectures-other-than-amd64` instead of resin or hypriot
- [Activate the i2c bus of Raspberry Pi 3 or Pi Zero W](https://diyprojects.io/activate-i2c-bus-raspberry-pi-3-zero/#.W2G7vtJKgdW)
- [Configuring GPIO - adafruit](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-4-gpio-setup/configuring-gpio)
- [Troubleshoot volume errors](https://docs.docker.com/storage/troubleshooting_volume_errors/)
	- some googling revealed that the issue with the GPIO could be that the docker container can't access the pins; this approach gives the container permission to mount a volume (`/dev/mem`) to get to a device's memory
- [docker run](https://docs.docker.com/v17.12/edge/engine/reference/commandline/run/)
- [modprobe in a docker container](https://dummdida.tumblr.com/post/117157045170/modprobe-in-a-docker-container)
