---
layout: post
title: Bikeshed v0.1
---

# First pre-release available
I've made the decision to make an pre-release version of Bikeshed over at Github, get it [here](https://github.com/DanEngelbrecht/bikeshed/releases).

## Channels
This release includes the "channels" feature which builds on the ideas I had for priority queues.

Channels can be used to build a Bikeshed that supports priorities but it is a bit more general than that, it can also be used to create per thread ready-queues with work-stealing, or it can be used to tag tasks that should be run a particular thread.

I started working on a blog post about the internals of Bikeshed and the new channels feature but it is not ready yet.

## Documentation
The header file has been updated with more information on how to use the API. There is also a bunch of test cases in the repo where you can see examples of usage.

## Tests
The tests has been validated to cover 100% of the code in `bikeshed.h` so it should be reasonably safe to use. If you find a bug or think something needs changing, feel free to ping me on twitter [@DanEngelbrecht](https://twitter.com/DanEngelbrecht) or leave an issue on [Github](https://github.com/DanEngelbrecht/bikeshed/issues).

## License
The code is released with the MIT license so feel free to do what you want with the code, no restrictions. I would appreciate some feedback if you find it useful though.
