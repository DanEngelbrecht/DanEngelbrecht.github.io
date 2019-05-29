---
layout: post
title: Bikeshed v1.0
---

# Version 1.0 of Bikeshed release
I decided to release version 1.0 of bikeshed as I feel I can commit to the API as it is now; get it here: [here](https://github.com/DanEngelbrecht/bikeshed/releases).

This release keeps the same API as the previous pre-release 4 and only has minor internal tweaks with no behavioral changes.

## Changelog

### Fixes
 - Use explicit int32_t for instead of long to ensure 32-bit values on GCC/Clang x64-builds
 - Corrected URL to blog in README.md
 - Added sample code for performance tests (in examples folder)
