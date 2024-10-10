---
id: quick-start
title: Quick Start（Web）
slug: quick-start
order: 1
---

OpenSumi is based on Node.js version `18.x +` and you need to make sure the correct version of Node.js installed locally. Also, OpenSumi relies on a number of Node.js Addons. To ensure these Addons are compiled properly, we recommend you to refer to the installation guide in [node-gyp](https://github.com/nodejs/node-gyp#installation) to set up your local environment.

![preview](https://img.alicdn.com/imgextra/i3/O1CN01uIRRRl1wmLkN9geV3_!!6000000006350-2-tps-2844-1830.png)

## Local Launch

> **Note: Because a large number of packages need to be downloaded during the compiling process, and some of them require the access to GitHub to download the source code, so please keep your access to GitHub open. Many 404 Not Found issues are caused by network access failures.**

Run the following commands in sequence:

```bash
$ git clone git@github.com:opensumi/ide-startup.git
$ cd ide-startup
$ yarn					   # install dependency
$ yarn start		       # start the front end and back end in parallel
```

Open the browser `http://127.0.0.1:8080` for preview or development.  

## Use Docker Image 

```bash
# pull the image  
docker pull ghcr.io/opensumi/opensumi-web:latest

# run
docker run --rm -d  -p 8080:8000/tcp ghcr.io/opensumi/opensumi-web:latest
```

Open the browser `http://127.0.0.1:8080`for preview or development.  

## Start the Parameters

The integration code in Startup is relatively simple. Basically it creates an instance of `ClientApp` and `ServerApp` respectively, introduces appropriate parameters and starts them. For detailed startup parameters, please see [integrated configuration](../universal-integrate-case/custom-config) document.  

## Customize IDE

OpenSumi helps customize basic capabilities including interface themes, built-in commands and menus by means of modules. You can view more detailed customization content at:

- [Integration configuration](../universal-integrate-case/custom-config)
- [Customize Comand](../universal-integrate-case/custom-command)
- [Customize Config](../universal-integrate-case/custom-config)
- [Customize view](../universal-integrate-case/custom-view)
