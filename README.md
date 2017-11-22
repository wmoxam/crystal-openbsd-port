# Crystal-lang for OpenBSD

Creates a package that installs [Crystal](https://crystal-lang.org/) and
its dependency manager [Shards](https://github.com/crystal-lang/shards)

## Build notes

Since Crystal is self-hosting this port relies on a precompiled object file
which is used to link a bootstrap compiler which is used to compile Crystal.

Building the object file yourself is easy and can be done on other systems
that support Crystal (Linux & OSX). After compiling the version of Crystal
that you want to use on OpenBSD, do the following:

```
.build/crystal build --cross-compile --target amd64-unknown-openbsdX.X src/compiler/crystal.cr -D i_know_what_im_doing
```

