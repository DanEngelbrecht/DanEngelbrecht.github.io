---
layout: post
title: Bikeshed v0.4
---

# Fourth pre-release available
The fourth pre-release version of Bikeshed has been released over at Github, get it [here](https://github.com/DanEngelbrecht/bikeshed/releases).

This release changes the `Bikeshed_AddDependencies` function making it possible to add one or more child dependencies to more than one task in a single call.

A function to free tasks has been added as well, `Bikeshed_FreeTasks` which should be used to back out of any created tasks. Tasks should still not be explicitly freed by the caller once they are readied but the API might be useful if a large structure with dependencies are created and do not fit in the shed and you need to back out.

`Bikeshed_ReadyCallback` has also changed and is now called on a per channel basis and includes which channel the readied tasks belong to.

Allocation and deallocation of tasks and dependecies has been refactored to be done in batches to reduce pressure on the heads of the index pools and ready heads. This leads to much better performance when creating more than one task or more than one dependency in a single call, better performance when readying more than one task in a single call and when multiple tasks are resolved as a result of a single task completing.

An option to align ready heads to cache lines to avoid contention between channels has been added with the `BIKESHED_L1CACHE_SIZE` macro. By default no alignement is done to keep the size of the shed down.

The last API addition is `BIKESHED_CPU_YIELD` which is used when a CAS operation fails, this evaluates to a mm_pause operation by default but can be customized or removed by defining it before including `bikshed.h`.

Finally there has been some bug fixes and cleanups and [Codacy](https://app.codacy.com/project/DanEngelbrecht/bikeshed/dashboard) analisys and badge has been added.

This looks like the last pre-release before calling it a 1.0 release where the API is finalized, the header has grown a bit 

## Changelog

### API changes
  - Bikeshed_AddDependencies to take array of parent task task_ids
  - Bikeshed_ReadyCallback now gets called per channel range

### API additions
  - Bikeshed_FreeTasks
  - BIKESHED_L1CACHE_SIZE to control ready head alignement - no padding/alignement added by default
  - BIKESHED_CPU_YIELD to control yielding in high-contention scenarios

### Fixes
  - Added default (non-atomic) implementations for helper for unsupported platforms/compilers
  - Codacy analisys and badge
  - More input validation on public apis when BIKESHED_ASSERTS is enabled
  - Batch allocation of task indexes
  - Batch allocation of dependency indexes
  - Batch free of task indexes
  - Batch free of dependency indexes
  - Fixed broken channel range detection when resolving dependencies
