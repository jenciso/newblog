---
title: How setup the Noto Color Emoji in your Ubuntu
description: Setting the color emoji unicode chars
published: true
comments: true
date: 2019-06-11
tags:
  - ubuntu
  - emoji
---

Ubuntu 18.04 LTS ships with all-new color emoji for use in messaging apps, text editors, and also on the web. Emoji is nothing new for Ubuntu. Older versions of Ubuntu come with simple black-and-white emoticons, but what about if you want to use the `color emoji` in Ubuntu 16.04 LTS.

In order to do that, you need to install a specific font to use it. Below, I show you the steps to accomplish this goal.


## Steps

* Download the Noto Color Emoji font from [here](https://www.google.com/get/noto), or if you prefer, you could download it directly from [here](https://noto-website-2.storage.googleapis.com/pkgs/NotoColorEmoji-unhinted.zip)

* Then, copy the ttf file into your `.fonts` dir

```shell
cd ~/Downloads
unzip NotoColorEmoji-unhinted.zip
mkdir ~/.fonts
mv NotoColorEmoji.ttf ~/.fonts
```

* Edit the `.fonts.conf` configuration file

```shell
vim ~/.fonts.conf
```

* And put the following content

```xml
<?xml version="1.0"?>
<!DOCTYPE fontconfig SYSTEM "fonts.dtd">
<fontconfig>
   <alias>
     <family>sans-serif</family>
     <prefer>
       <family>Noto Color Emoji</family>
       <family>Noto Emoji</family>
     </prefer>
   </alias>

   <alias>
     <family>serif</family>
     <prefer>
       <family>Noto Color Emoji</family>
       <family>Noto Emoji</family>
     </prefer>
   </alias>
</fontconfig>
```

* You need to rebuild the cache fonts to take effect

```shell
sudo fc-cache -fv
```

> In chrome web browser you need to restart that application. For your Gnome Terminal, some characters would be a little big, so you should setup your Profile Terminal to specify the specific size font to use, in my case `size 13` 
 
