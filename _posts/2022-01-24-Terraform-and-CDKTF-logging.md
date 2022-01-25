---
title: Terraform and CDKTF logging
layout: post
permalink: /2022/01/24/Terraform-and-CDKTF-logging
date: 2022-01-24 12:00:00 +0200
author: Jyrki Heinonen
categories: IaC
tags: Terraform CDKTF Logging
---

I was working on a simplish enough Azure project and used CDK for Terraform for the first time. I had used Terraform previously so the main concept was clear. Suddenly my stack would not deploy all the way through and no error message was given. 
<!--more-->

![Renewal](/assets/img/2022/01/cdktf-trace-logging.png)

Usually when the 'CDKTF Deploy' run stops unexpected something went wrong in Azure and the error message is displayed in the terminal. But for this particular crash, it did not print out anything :| No errors, just the message of:

```
Warning: Remember methods should be a pure function of props and state, triggering nested component updates from render is not allowed. If necessary, trigger nested updates in ComponenDidUpdate.
```

This puzzled my mind for some time and I tried to find the flaw in my configuration, but to no avail... For some reason I had completely forgotten about the basic debugging steps of logging :D 

After some binging, I found the correct parameters to set to enable logging.

``` Powershell
# Session variables
$env:CDKTF_LOG_LEVEL = "TRACE"
$env:TF_LOG = "TRACE"

# Permanent user variables
setx CDKTF_LOG_LEVEL TRACE
setx TF_LOG TRACE
```

``` Bash
# Session variables
export CDKTF_LOG_LEVEL="TRACE"
export TF_LOG="TRACE"
```

I spent a good couple hours diagnosing this and trace-level logging helped me to find the small flaw in the logic that for some reason didn't output the error correctly as usually... Phew!!! I had to turn this on couple of times already to find out the errors that were making my stack creation to fail.

Hopefully next time I remember better and use these earlier :)

Documentation:
- [Terraform](https://www.terraform.io/internals/debugging)
- [CDK TF](https://github.com/hashicorp/terraform-cdk/blob/main/docs/cli-commands.md)