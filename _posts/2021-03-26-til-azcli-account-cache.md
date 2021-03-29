---
title: TIL - AzCli shows subscriptions that you are not logged into?
layout: post
permalink: /2021/03/26/til-azcli-account-cache
date: 2021-03-26 12:00:00 +0200
author: Jyrki Heinonen
categories: TIL AzCli cache
tags: TIL AzCli
---
Lately I've been wondering why AzCli sometimes shows subscriptions that I'm not logged into and was "worried" that I could accidentaly run commands to a wrong subscription... Like delete customers resourcegroups or something like that 🤕

### Today I learned that
AzCli caches many things and you can clear it if you really want...

```
az logout
az account clear
az login
```

"Problem" solved!
