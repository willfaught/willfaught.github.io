+++
categories = ["life"]
date = 2007-10-30T17:36:00-07:00
tags = ["ideas", "ipod", "linux", "mpeg-4", "transcoding", "ubuntu", "video", "vlc"]
title = "IPod Video Transcoding In Linux"
+++

I've cobbled together a pretty good tool for [transcoding](https://en.wikipedia.org/wiki/Transcode) video in pretty much any format into the [MPEG-4](https://en.wikipedia.org/wiki/MPEG-4) format that iPods play. You have to have the [VLC media player](https://www.videolan.org/) installed because it does the actual transcoding. So in other words, I've researched a program that was already out there and made an easy wrapper script. Damn I'm smart. Yay modularity! Seriously though, there aren't a lot of guides out there that are easy to follow. Maybe someone will find this useful. It works great on [Ubuntu](https://en.wikipedia.org/wiki/Ubuntu_%28Linux_distribution%29) Gutsy Gibbon 7.10.

Here's my bash script:

```bash
#!/bin/bash
extension=`echo "$1" | awk -F . '{print $NF}'`
name=`basename "$1" ."$extension"`
vlc -vvv "$name.$extension" \
    --sout "#transcode{vcodec=mp4v, \
    vb=1024, \
    scale=1, \
    acodec=mp4a, \
    ab=128, \
    channels=2}:standard{access=file, \
    url=$name.mp4}" \
    --aspect-ratio "4:3" \
    --sout-transcode-width 360 \
    --sout-transcode-height 240 \
    --sout-transcode-fps 30 \
    2>/dev/null
```
