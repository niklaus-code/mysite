---
title: node-saas
date: 2025-02-06 14:23:23
tags:
    前端
---
```bash
gyp: Undefined variable standalone_static_library in binding.gyp while trying to load binding.gyp
gyp ERR! configure error 
gyp ERR! stack Error: `gyp` failed with exit code: 1
gyp ERR! stack     at ChildProcess.onCpExit (/home/dbe/DBE_web/node_modules/node-gyp/lib/configure.js:345:16)
gyp ERR! stack     at ChildProcess.emit (node:events:524:28)
gyp ERR! stack     at ChildProcess._handle.onexit (node:internal/child_process:293:12)
gyp ERR! System Linux 4.18.0-513.24.1.el8_9.x86_64
gyp ERR! command "/usr/local/node-v22.13.0-linux-x64/bin/node" "/home/dbe/DBE_web/node_modules/node-gyp/bin/node-gyp.js" "rebuild" "--verbose" "--libsass_ext=" "--libsass_cflags=" "--libsass_ldflags=" "--libsass_library="
gyp ERR! cwd /home/dbe/DBE_web/node_modules/node-sass
gyp ERR! node -v v22.13.0


yarn add node-sass
```
