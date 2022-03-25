---
title: Terraform and CDKTF logging
layout: post
permalink: /2022/01/24/Terraform-and-CDKTF-logging
date: 2022-01-24 12:00:00 +0200
author: Jyrki Heinonen
categories: IaC
tags: Terraform CDKTF Logging
---

I had a simple project 
<!--more-->

![Renewal](/assets/img/2022/01/cdktf-trace-logging.png)



``` Powershell
##############
# Powershell #
##############

# Session variables
$env:CDKTF_LOG_LEVEL = "TRACE"
$env:TF_LOG = "TRACE"

# Permanent user variables
setx CDKTF_LOG_LEVEL TRACE
setx TF_LOG TRACE
```

``` Bash
########
# Bash #
########

# Session variables
export CDKTF_LOG_LEVEL="TRACE"
export TF_LOG="TRACE"
```

I spent a good couple hours diagnosing this and trace-level logging helped me to find the small flaw in the logic that for some reason didn't output the error correctly as usually... Phew!!!

Documentation:
- [Terraform](https://www.terraform.io/internals/debugging)
- [CDK TF](https://github.com/hashicorp/terraform-cdk/blob/main/docs/cli-commands.md)