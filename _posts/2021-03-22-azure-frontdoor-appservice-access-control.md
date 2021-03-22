---
title: How to restrict access to appservice from certain frontdoor instance
layout: post
permalink: /2021/03/21/restrict-appservice-access-to-certain-frontdoor
date: 2021-03-21 22:00:00 +0200
author: Jyrki Heinonen
categories: azure frontdoor
tags: azure frontdoor appservice access-control
---

This post describes the use of Azure Front Door in front of App Service instead of application gateway or some other
<!--more-->

Usually in all public web-projects we have a need to secure the actual application from the public internet or at least have somewhat control of the visible endpoints. Sometimes we used nginx-server, F5 or application gateway from azure or any other reverse-proxy application to do the job but recently have have used Azure Front Door for it. 

Documentation describes Front Door like this:
> Azure Front Door is a global, scalable entry-point that uses the Microsoft global edge network to create fast, secure, and widely scalable web applications.
> https://docs.microsoft.com/en-us/azure/frontdoor/front-door-overview

Azure Front Door is really easy and fast to provision and configure to your needs. Things I liked the most are:
- Global, Microsoft managed resource -> Nothing to install and maintenance
- Support for custom domain (only CNAME alias needed)
- SSL certificate management (we can use Front Door provisioned and managed certificates instead of our own from keyvault)
- SSL Offloading and HTTP->HTTPS redirection
- Basic setup takes only couple of minutes

## Architecture
![Architecture](/assets/img/2021/03/22/architecture.png)

## Settings
TBD

## The problem
Nice! Our reverse-proxy is working and our application is secure... Or is it? Because Front Door is global service all of Front Door instances communicate via the same IP addresses allowed in your appservice access restrictions.

![App Service Access Control](/assets/img/2021/03/22/appservice-access.png)

## And finally the solution
Previously your application had to be modified to support host header injected by Front Door. The header is *X-Azure-FDID*. You had to modify your application to read this header and compare it against value configured somewhere in the application that the request is really coming from the Front Door you created.

Fortunately nowadays there is a better and simpler way!

Microsoft have added direct support in app service access control rules for defining this host header. The documentation can be found [here](https://docs.microsoft.com/en-us/azure/app-service/app-service-ip-restrictions#restrict-access-to-a-specific-azure-front-door-instance-preview) and while it's still in preview, I'd be quite easy to implement this rule because it is so heavily demanded just for this purpose.

And finally you can also set it directly from the portal. Just go to app service -> networking -> Access Restrictions. Add a new rule with the correct Front Door ID and you can get rid of the other Front Door address block! And here's the setting in the portal:

![App Service Access Control rules](/assets/img/2021/03/22/appservice-access-2.png)

Thanks for reading!
