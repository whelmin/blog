[English](./README.md) | **中文**

<h1 align="center">Hui Wen's Blog</h1>

## 依赖

* [docsify-cli](https://www.npmjs.com/package/docsify-cli)
* [Disqus](https://disqus.com/)
  
本项目通过 docsify-cli 快速创建项目结构，支持本地预览文档，另外利用 Disqus 提供评论系统支持。

## 目录结构

```
.
├── .gitignore                  // git忽略文件
├── README.md                   // README
├── README.zh-CN.md             // README.zh-CN
└── docs                        // docsify 初始化目录
    ├── _coverpage.md           // 封面页配置文件
    ├── _navbar.md              // 导航栏配置文件
    ├── _sidebar.md             // 侧边栏配置文件
    ├── .nojekyll               // 用于阻止 GitHub Pages 会忽略掉下划线开头的文件
    ├── index.html              // 入口文件，也是配置文件
    ├── README.md               // 渲染主页内容
    ├── advanced                // 文章 - 前端进阶
    ├── base                    // 文章 - 前端基础
    ├── book                    // 文章 - 书籍
    ├── es6                     // 文章 - ES6 基础
    ├── interesting             // 文章 - 有趣的题目
    ├── vue2                    // 文章 - Vuejs 2
    └── ...                     // 文章 - 持续更新中...

```

## 本地预览

```
cd docs/
docsify serve ./ -o
```

## 部署

本项目部署在 `GitHub Pages` 上，GitHub Pages 支持从项目的 `docs/` 文件夹读取文件。
