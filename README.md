# README

## 依赖

本项目依赖 `docsify-cli` , 通过 docsify 的 脚手架快速创建项目结构，本地预览文档

## 目录结构

```
.
├── .gitignore                  // git忽略文件
├── docs                        // docsify初始化目录
|   ├── _media                  // 静态资源文件，如引用图片
|   ├── _coverpage.md           // 封面页配置文件
|   ├── _sidebar.md             // 侧边栏配置文件
|   ├── .nojekyll               // 用于阻止 GitHub Pages 会忽略掉下划线开头的文件
|   ├── index.html              // 入口文件
|   ├── README.md               // 渲染主页内容的文件
|   └── post                    // 文章文件夹
|       ├── base                // 前端基础文章
|       ├──  vue                // vue.js相关文章
|       └── es6                 // es6基础文章
|       └── ...                 // es6基础文章
└── README.md // 项目简介

```

## 本地预览

```
docsify serve docs
```

## 部署

本项目部署在 `GitHub Pages` 上，GitHub Pages 支持从项目的 `docs/` 文件夹读取文件。
