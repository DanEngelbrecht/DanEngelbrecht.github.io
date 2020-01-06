---
layout: post
title: Longtail Basics
---

# What is longtail
Delivering big content (games as an example) is starting to become a problem, we used to have games on floppy disks, then that grew to multiple discs. Then came DVD, multi-dvd and Blueray. We now using the internet for download and even if you have a fast fiber connection, downloading 200 Gb of game data before being able to play (hello RDR2) is not a great experience.

Delivering updates (or DLCs) is also painful, have you ever updated Fortnight on a mobile device? Feels like you need to download a couple of gigabytes every other week or so.

Now, games have big content so we can't get away from that, but what if we could download content more incrementally? I'm not talking about streaming data of the internet but you probably don't need the full 200 Gb of data just to get started, right?

This has been done before but it is not very wide spread. Don't know why, but there are certainly reasons. It is more complicated to do incremental stuff than delivering a big package with all you need. Or perhaps you don't want to have your internet connection ready to download whenever you need more data.

At my work (embark-studios.com) we also do daily play test, once every morning at least and we now need to download and decompress about 20 Gb each morning just to be able to join the play test. And the game is growing at a rapid pace so things will only get worse.

The idea with the Longtail project is to build a library that makes it easier to deliver "just what you need" and download the content on demand. And also reduce and simplify updates and content changes in size. The way data is stored is geared towards fast SSD storage which is becoming the norm today where seek times are fast.

# Concepts
Longtail separates the "structure" of your data - the content layout - names of assets, how data is organized in folders from the actual data. This information is called a `Version Index`. It contains paths (names) for the assets, the size of the asset, a hash of the content and each asset has a list of chunk hashes to identify what data the asset contain. The `Version Index` is stored separately from the data of the content.

The content of each asset is stored in `chunks` inside `blocks`.

A chunk is identified by the hash of its data, multiple assets can point to the same chunks or use some of the chunks from one asset and other chunks from another asset combined with its own chunks. This removes redundant data and as each chunk data is identified by its hash it is easy to remove duplicates.

Chunks are not directly accessible as they are stored in `blocks`. Blocks are a collection of `chunks` - one or more, this is so each piece of data can be close to an ideal size for download etc. It also helps compression if each piece of data to compress is a bit larger. Chunks are not compressed individually, they are compressed on a per block basis.

To find a chunk of an asset you need to know which block it is stored in, this is done using the `Content Index`. The `Content Index` lists all the blocks it "knows" and chunks inside each block. It does **not** contain any paths or information which chunks belongs to a certain asset. A `Content Index` can be constructed from a set of existing blocks or built up when creating blocks from a `Version Index`.

Think of the data chunks as a soup of data with `Content Index` as a lookup of unique data blobs and the `Version Index` as a view of that data that spans all, some or more than what you have locally on your disk.

Both the `Version Index` and the `Content Index` are designed to be easy to diff, both to get the difference between two versions, finding which chunks are needed to move from one version to a new one, diffing what content you have locally from what is on a remote server and finding which blocks are needed to get a complete set of chunks for specific version.

# How would you use it?
There are basically two ways it can be used, the first one is not fully implemented and was the initial idea for how to use it - as a virtual file system where you quickly could download a `Version Index`, request the blocks needed to fulfill the version. The dowloading updates or extra data via a new `Version Index` and getting the diff in blocks to accomodate the new version. I had ideas of implementing for example a file system for Unreal, but that is quite a bit of work and requires thorough testing to get right.

The second way of using it is to "extract" a version onto disk - rebuilding the version as it looked on disk before it got indexed, chunked and blocked up in Longtail. At Embark Studios we use a tool called desync which is a Go implementation og casync to do just that. The problem with desync is that it is fine if what you are syncing is not to big and if each file is reasonably small. An Unreal game where you use .pak files is not the ideal use case for desync/casync. Both desync and casync rebuilds the folder structure from scratch with each deployment, it is basically a smarter rsync but without the ability to modify a folder structure in place.

In order to test out Longtail I decided to see how it would work to use it to syncronize a folder using a `Version Index` and content in the form of blocks. Longtail is written to use multi threading as much as it can and it does some tricks to make it fast even if the content has a few very big files. In this scenario you have a target `Version Index`, a local cache of blocks indexed into a `Content Index`, a remote store with all the blocks for all versions and the ability to request a `Content Index` for the remote data.

So far it looks promising in terms of speed and the size of the delta between versions matches desync/casync but it is very much faster doing an incremental update of an existing version as it can add, delete and modify just those files that has changed in a folder structure.

# Can I use it in my project today
No, I would not recommend it - it works and is reasonably tested but only the core functionality is in place. There are not even very good examples on how to use it right now, but if you are interested poke around the code and I will continue to build upon this as long as it shows promise.

The current focus is trying to build a Go module which includes the C code for easier distribution so I can write a client for syncing folders that supports different backend for remote storage such as HTTPS or GCS. I'm trying to keep the core stuff in very portable C and add on top of it instead of building out the library with stuff that is better suited to write in other languages. Starting with Go as that is one of the languages that is easy integrate with C and has a good echo system of libraries.

# Why write it in C?
Why not Go, Rust or C++?

Well I have been writing code professionally for over thirty years now and I guess I'm a bit stuck in my ways. No, not really, but I wanted the core library to be highly portable and easy to integrate into other languages. I also prefer C over C++ as it is a less complex language and compiles really fast. Adding C code to Go, Rust and C++ is easy. I do not have a lot of experience with Go or Rust but I'm a bit curious how it would look to write this in Rust at some point.

Writing it in C makes it very acessible (in my opinion) for someone to easily integrate or to look at the code and implement it in another language. Everybody knows C right?

# What's next?
I'm currently working on a Go front end to make a command line tool that can syncronize a folder from a remote store such as GCS in one go, this will let me evaluate how it compares in a real live setting to desync that we use at Embark Studios currently. The Go frontend would also allow you to create new versions and upload the blocks to distribute it.
