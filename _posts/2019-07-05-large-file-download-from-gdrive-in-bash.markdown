---
title: Large file download from Gdrive in bash
layout: post
categories: snippets
---

Setup:

Add this code to your ~/.bash_aliases file.
```bash
function gdrive_download () {
  CONFIRM=$(wget --quiet --save-cookies /tmp/cookies.txt --keep-session-cookies --no-check-certificate "https://docs.google.com/uc?export=download&id=$1" -O- | sed -rn 's/.*confirm=([0-9A-Za-z_]+).*/\1\n/p')
  wget --load-cookies /tmp/cookies.txt "https://docs.google.com/uc?export=download&confirm=$CONFIRM&id=$1" -O $2
  rm -rf /tmp/cookies.txt
}
```
Open new bash session.
Usage:
```bash
gdrive_download long_google_drive_file_id filename.ext
```
[source](https://gist.github.com/iamtekeste/3cdfd0366ebfd2c0d805#gistcomment-2359248)