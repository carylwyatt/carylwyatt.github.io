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
