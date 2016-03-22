---
title: js的模块化
date: 2016-03-07 17:32:46
tags: [AMD, requirejs, CMD, seajs, commonJS, browserify, UMD, 模块化, 前端工程化]
---

| AMD规范  | CMD规范  | commonJS规范 | UMD规范 | ES6规范 |
| :------------ |:---------------:| :---------------:|:---------------:|-----:|
| 异步      | 按需加载 | 同步 | 判断 | 混合 |
| 动态加载   | 动态加载 | 静态加载 | 判断 | 混合|
| 前置      | 就近 | - | - | 声明&import |
| 前端      | 前端        | 后端/前端+browserify | AMD/commonJS/全局模块 | both |
| require.js | sea.js        | - | - | TS |
