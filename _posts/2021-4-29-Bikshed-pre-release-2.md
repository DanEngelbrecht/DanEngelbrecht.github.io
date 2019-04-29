---
layout: post
title: Bikeshed v0.2
---

# Second pre-release available
The second pre-release version of Bikeshed has been released over at Github, get it [here](https://github.com/DanEngelbrecht/bikeshed/releases).

## Fixes
- Internal cleanups
- Fixed warnings and removed clang warning suppressions
  - `-Wno-sign-conversion`
  - `-Wno-unused-macros`
  - `-Wno-c++98-compat`
  - `-Wno-implicit-fallthrough`
- Made it compile cleanly with clang++ on Windows
