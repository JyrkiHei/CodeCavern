---
title: Performance problems adding users to Azure AD groups
layout: post
permalink: /2022/04/20/adding-users-to-aad-groups
date: 2022-04-20 12:00:00 +0200
author: Jyrki Heinonen
categories: ActiveDirectory AzCli
tags: AzCli
---

I was creating a small script for adding users to a Azure Active Directory group. I tested the script in my Azure tenant with only handfull of users and naturally it worked fine. But when running in different environment with 100k+ users, I let it run for 5 minutes and it was still looking for the users. 
<!--more-->

My script was like this in the beginning:

``` bash
# Login needed of course
az login
az account set -s 123456789-1234-1234-1234-123456789012
 
# Variables
$group = "group-name"
$userEmail = "useremail@company.com"
$userId = $(az ad user list --query "[?userPrincipalName=='$userEmail'].objectId" --output tsv)
 
# OPTION: Add as a owner
az ad group owner add --group $group --owner-object-id $userId
 
# Add as a member
az ad group member add --group $group --member-id $userId
```

After a quick search fortunately someone had alreade blogged about this same problem. I had completely skipped the part from documentation that states 'The Azure CLI uses the --query argument to execute a JMESPath query on the results of commands ... Queries are executed on the JSON output before any display formatting'. The main part being 'queries are executed on the JSON output'... sigh...

With the help of this [blog post](https://yourazurecoach.com/2021/02/10/resolving-the-azure-ad-object-id-with-azure-cli-takes-ages/) I was able to solve the problem. Query is running on the output of the commands (client-side), but the filter-parameter is doing an OData-filtering and running server-side. I just had to modify the 'az ad user list' command a bit.

``` bash
$userId = $(az ad user list --filter "(userPrincipalName eq '$userEmail')" --query "[].objectId" --output tsv)
```	

After that everything worked fine! The script only took couple of seconds instead of minutes.
