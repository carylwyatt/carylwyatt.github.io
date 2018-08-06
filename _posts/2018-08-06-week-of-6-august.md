---
layout: post
title: "week of 6 august"
date: 2018-08-06
---
I talked with Scott over the weekend about npm packages and dependencies. I'm going to try taking the `^` character out of the `package.json` file for packages I want to use old stuff with. I learned that the `^` means npm will install *newer* versions of the package if there aren't breaking changes (major update). That's not what I want. 
- removed carat from `package.json`
- ran `npm install` and everything is fine?
- uninstalled johnny-five and serialport and installed v 0.12.0 of johnny-fve
- same errors: serialport @6.0.5 no prebuilt binaries for node 8.11.3, arch=arm, platform=linux
- **big duh moment**: [support for raspi linux with arch=arm](https://www.npmjs.com/package/serialport#raspberry-pi-linux) doesn't exist
- how does johnny-five have serialport as a dependency if it doesn't work on linux ARM platforms?!
