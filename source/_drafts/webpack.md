---
title: webpack Invalid configuration object
date: 2017-02-07 11:02:36
categories: node
tags:  webpack
---
## webpack error
using ruanyf a boilerplate for [React-Babel-Webpack project](https://github.com/ruanyf/react-babel-webpack-boilerplate)
`Invalid configuration object. Webpack has been initialised using a configuration object that does not match the API schema.
 - configuration.entry should be one of these:
    object { <key>: non-empty string | [non-empty string] } | non-empty string | [non-empty string] | function
       The entry point(s) of the compilation....`

## webpack issuse close
[see here](https://github.com/angular/angular-cli/issues/2234)
`npm uninstall webpack --save-dev`

