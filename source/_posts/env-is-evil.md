---
layout: post
title: "ycmd server SHUT DOWN Unexpected exit code -11"
date: 2018-11-27 07:30:01 +0800
comments: true
categories: vim
tags:
    - ycm
---
## What issue?
The ycmd server SHUT DOWN (restart with ':YcmRestartServer'). Unexpected exit code -11.

## try fixed

```shell
./install.py --clang-completer
cd ~/.vim/bundle/YouCompleteMe/third_party/ycmd
cd ycmd
cp default_settings.json ..
python ycmd --options_file default_settings
python3 ./build.py
```

 Not working.

So reading code, in build.py  find using sysconfig.

I try add  pdb.set_trace() in function GetPossiblePythonLibraryDirectories first line. 

In pdb shell p sysconfig.get_config_var("LIBPL"), result show  path is fault, not  mine.

I try hard code absolute path in GetPossiblePythonLibraryDirectories, but no working.

## fixed

Anyway,  in ycm issues find miniconda3 will conflict.

so try:

```python
rm -rf miniconda3
python3 install.py
```

well, it's working.

Environment implicit is evil,  explicit is better than implicit.

