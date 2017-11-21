---
layout: post
title: "mix install error"
date: 2017-11-20 23:26:42 +0800
comments: true
categories: elixir
tags: elixir
---
## install phoenixframework
using mix install phoenixframework  have error

    mix archive.install https://github.com/phoenixframework/archives/raw/master/phx_new.ez
    Are you sure you want to install "https://github.com/phoenixframework/archives/raw/master/phx_new.ez"\? [Yn] Y
    Assertion failed: (ctx), function digest_update, file /BuildRoot/Library/Caches/com.apple.xbs/Sources/boringssl/boringssl-109.1.6/apple/crypto/digests.c, line 49.
    [1]    1204 abort      mix archive.install

Darwin PycleardeMacBook-Pro.local 17.0.0 Darwin Kernel Version 17.0.0: Thu Aug 24 21:48:19 PDT 2017; root:xnu-4570.1.46~2/RELEASE_X86_64 x86_64

## [search error message](http://mail-archives.apache.org/mod_mbox/couchdb-user/201709.mbox/%3C1735214007.1758.1506641159142.JavaMail.Joan@RITA%3E)
[This is an Erlang bug](https://bugs.erlang.org/browse/ERL-439)
[This commit fixed the problem](https://github.com/erlang/otp/commit/882c90f72ba4e298aa5a7796661c28053c540a96)

## solved
brew upgrade erlang


