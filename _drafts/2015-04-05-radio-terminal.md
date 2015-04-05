---
published: false
---

## How to I streamed an online radio station through the terminal

the radio station in this post is the quran radio station at [Quran Radio Website](http://www.ertu.org/quran/QuranKareem.html).in this post I will explain in detail how I could stream the station through the terminal. if you just want to know the how-to skip to the end of this post.

## The website
in the beginning, I wanted to get the streaming link the website uses to recieve the data. the website uses [JWPlayer](http://www.jwplayer.com/) to stream the radio.Using the web browser's developer tools I found the script used to stream the radio station. the script included the url of the streamed data. after that I inspected the network tab. I found out that the website uses [RTMP](http://en.wikipedia.org/wiki/Real_Time_Messaging_Protocol) protocol to communicate with the server.

## RTMP
after some google search, I stumbled upon rtmpdump [rtmpdump](https://rtmpdump.mplayerhq.hu/). as the name suggests, it dumps a stream of rtmp station into a file or the stdin.But there is a catch. there are some parameters you need to set otherwise you will get a connection error.so how do I get the parameters? enter [wireshark](https://www.wireshark.org/).

## wireshark
through _wireshark_, I tracked the packets with the _RTMP_ protocol.
