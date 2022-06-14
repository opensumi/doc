---
id: electron-quick-start
title: Quick Start（Electron）
order: 2
---

OpenSumi integrates internally a simple Electron framework, and designed to provide a desktop environment to quick test OpenSumi. You can build your own client by using [idee-electron](https://github.com/opensumi/ide-electron) as a template.

You can also get the relevant installation package in the [Release](https://github.com/opensumi/ide-electron/releases) list for a quick experience.

![preview](https://img.alicdn.com/imgextra/i4/O1CN013APO901bevPEe8Ydx_!!6000000003491-2-tps-2478-1624.png)

## Compatible Environments 

- Electron 11.4.3+
- macOS & Windows 7+
- Node.js 12+

## Local Launch

> **Note: Because a large number of packages need to be downloaded during the compilation process, and some packages need to access GitHub to download the source code, please keep GitHub accessible. Many 404 Not Found problems are caused by network access failures.**   
> China mainland users who cannot install dependencies properly due to network problems can switch to the `main-cn` branch first: `git checkout main-cn`, or refer to the appendix at the end of the article to configure the npm image.

Run the following commands in sequence:

```bash
$ git clone git@github.com:opensumi/ide-electron.git
$ cd ide-electron
$ yarn
$ yarn build
$ yarn rebuild-native -- --force-rebuild=true
$ yarn download-extension # install built-in extensions (optional)  
$ yarn start
```

## Development 

Run in the project root directory

```bash
$ yarn watch
```

Start

```bash
$ yarn start
```

## Package

Run `yarn pack` to package the project. The installation package will be exported in the `out` directory.   


## npm Image Configuration
Open `ide-electron/.npmrc` and add the following NPM image configuration to resolve installation dependency failure:  
```config
registry=https://registry.npmmirror.com/
disturl=https://npmmirror.com/mirrors/node
chromedriver-cdnurl=https://npmmirror.com/mirrors/chromedriver
couchbase-binary-host-mirror=https://npmmirror.com/mirrors/couchbase/v{version}
debug-binary-host-mirror=https://npmmirror.com/mirrors/node-inspector
flow-bin-binary-host-mirror=https://npmmirror.com/mirrors/flow/v{version}
fse-binary-host-mirror=https://npmmirror.com/mirrors/fsevents
fuse-bindings-binary-host-mirror=https://npmmirror.com/mirrors/fuse-bindings/v{version}
git4win-mirror=https://npmmirror.com/mirrors/git-for-windows
gl-binary-host-mirror=https://npmmirror.com/mirrors/gl/v{version}
grpc-node-binary-host-mirror=https://npmmirror.com/mirrors
hackrf-binary-host-mirror=https://npmmirror.com/mirrors/hackrf/v{version}
leveldown-binary-host-mirror=https://npmmirror.com/mirrors/leveldown/v{version}
leveldown-hyper-binary-host-mirror=https://npmmirror.com/mirrors/leveldown-hyper/v{version}
mknod-binary-host-mirror=https://npmmirror.com/mirrors/mknod/v{version}
node-sqlite3-binary-host-mirror=https://npmmirror.com/mirrors
node-tk5-binary-host-mirror=https://npmmirror.com/mirrors/node-tk5/v{version}
nodegit-binary-host-mirror=https://npmmirror.com/mirrors/nodegit/v{version}/
operadriver-cdnurl=https://npmmirror.com/mirrors/operadriver
phantomjs-cdnurl=https://npmmirror.com/mirrors/phantomjs
profiler-binary-host-mirror=https://npmmirror.com/mirrors/node-inspector/
python-mirror=https://npmmirror.com/mirrors/python
rabin-binary-host-mirror=https://npmmirror.com/mirrors/rabin/v{version}
sass-binary-site=https://npmmirror.com/mirrors/node-sass
sodium-prebuilt-binary-host-mirror=https://npmmirror.com/mirrors/sodium-prebuilt/v{version}
sqlite3-binary-site=https://npmmirror.com/mirrors/sqlite3
utf-8-validate-binary-host-mirror=https://npmmirror.com/mirrors/utf-8-validate/v{version}
utp-native-binary-host-mirror=https://npmmirror.com/mirrors/utp-native/v{version}
zmq-prebuilt-binary-host-mirror=https://npmmirror.com/mirrors/zmq-prebuilt/v{version}
bin-mirrors-prefix=https://npmmirror.com/mirrors
canvas_binary_host_mirror=https://npmmirror.com/mirrors/canvas
electron-mirror=https://npmmirror.com/mirrors/electron/
electron_custom_dir={{ version }}
electron_builder_binaries_mirror=https://registry.npmmirror.com/-/binary/electron-builder-binaries/
```