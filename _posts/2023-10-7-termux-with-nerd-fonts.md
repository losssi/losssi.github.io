---
layout: single
tags: termux
title: Install Nerd Fonts to Termux
related: true
comments: true
header:
  teaser: /assets/images/posts/Termux.jpg
---

![](/assets/images/posts/Termux.jpg)
**Looks cool and enables _glyphs_ for oh-my-posh prompt utility locally and also for oh-my-posh on a remote server**

***Time: 10 min***

## 1 Cd to .termux
```shell
cd ~/.termux
```

## 2 Install wget
```shell
pkg install wget
```

## 3 Get a nerd font
*In [https://github.com/ryanoasis/nerd-fonts/releases/download/v3.0.2/](https://github.com/ryanoasis/nerd-fonts/releases/download/v3.0.2/) you find lots of fonts. I opted for Hack*

```shell
wget https://github.com/ryanoasis/nerd-fonts/releases/download/v3.0.2/Hack.zip
```

## 4 Install unzip if you dont have it already
```shell
pkg install unzip
```

## 5 Unzip and copy the font as font.ttf
```shell
unzip Hack.zip
cp HackNerdFontPropo-Regular.ttf font.ttf
```

## 6 Reload Temux settings
```shell
termux-reload-settings
```

## 7 Exit and restart Termux
