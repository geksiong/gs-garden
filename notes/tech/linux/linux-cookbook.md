---
title: Linux Cookbook
date: 2020-09-03

tags:
  - linux
  - shell
---

# Linux Cookbook

## Convert all DOS-format files to Unix

```shell
find -type f -exec grep -Il . {} \; | xargs -I {} dos2unix -k {}
```

Note: need to use a recent version of dos2unix to avoid corrupting unicode files...

## Send email via curl

```shell
curl --url 'smtps://<mail-server>:465' --ssl-reqd \
  --mail-from '<from-email>' \
  --mail-rcpt '<to-email>' \
  --user '<user>:<password>' \
  -T <(echo -e 'From: <from-email>\\nTo: <to-email>\\nSubject: Curl Test\\n\\nHello')
```

For port 25, use `smtp://<mail-server>:25`

## Parse JSON and YAML files

## Parse CSV files
