---
title: CDKTF get and problems with Github actions
layout: post
permalink: /2022/03/25/CDKTF-problems-github-actions
date: 2022-03-25 12:00:00 +0200
author: Jyrki Heinonen
categories: IaC
tags: Terraform CDKTF Github-Actions
---

I tried to create my Terraform CDKTF IaC pipelines using Github actions, but was greeted with an error when fetching providers and modules.
<!--more-->

The action steps I was doing were really basic. Just setting up Terraform CLI and running CDKTF-CLI (In previous step I had run 'npm install' already).

``` yaml
# Github action steps

- name: HashiCorp - Setup Terraform
  uses: hashicorp/setup-terraform@v1

- name: CDKTF - Load modules and providers
  run: cdktf get
```

And I was greeted with this lovely message!

``` yaml
> Run cdktf get
SyntaxError: Unexpected token c in JSON at position 1
    at JSON.parse (<anonymous>)
    at /opt/hostedtoolcache/node/16.14.0/x64/lib/node_modules/cdktf-cli/bundle/bin/cmds/handlers.js:53:29993
    at processTicksAndRejections (node:internal/process/task_queues:96:5)
    at async I5e (/opt/hostedtoolcache/node/16.14.0/x64/lib/node_modules/cdktf-cli/bundle/bin/cmds/handlers.js:43:855)
    at async TX (/opt/hostedtoolcache/node/16.14.0/x64/lib/node_modules/cdktf-cli/bundle/bin/cmds/handlers.js:53:29820)
    at async xbe.generateTypeScript (/opt/hostedtoolcache/node/16.14.0/x64/lib/node_modules/cdktf-cli/bundle/bin/cmds/handlers.js:60:6114)
    at async xbe.generate (/opt/hostedtoolcache/node/16.14.0/x64/lib/node_modules/cdktf-cli/bundle/bin/cmds/handlers.js:60:6376)
    at async /opt/hostedtoolcache/node/16.14.0/x64/lib/node_modules/cdktf-cli/bundle/bin/cmds/handlers.js:630:13293

⠇ downloading and generating modules and providers...

Unexpected token c in JSON at position 1

Error: Process completed with exit code 1.
```

I spent too much time diagnosing this one and Bing searches did not help (nor did google searches!). After I managed to guess some good search words from JSON parsing and Terraform I found this [issue](https://github.com/hashicorp/terraform-cdk/issues/1013) in Github.

![Issue](/assets/img/2022/03/github-json-issue.png)

So after some facepalming and a quick hour-long coffee break I changed my steps like this.

``` yaml
# Github action steps

- name: HashiCorp - Setup Terraform
  uses: hashicorp/setup-terraform@v1
  with:
    terraform_wrapper: false # Default: true
    # Breaks the build! 
    # https://github.com/hashicorp/terraform-cdk/issues/1013

- name: CDKTF - Load modules and providers
  run: cdktf get
```

After that everything worked! Well, everything expect the "new" OIDC authentication to Azure, but that's another story
