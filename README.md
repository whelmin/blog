**English** | [中文](./README.zh-CN.md)

<h1 align="center">Hui Wen's Blog</h1>

## Dependencies

This project depends on [docsify-cli](https://www.npmjs.com/package/docsify-cli) , through this scaffold to quickly create a project structure, preview documents locally, use [Disqus](https://disqus.com/) Add comments to each article.

## Tree of project

```
.
├── .gitignore                  // git ignore config
├── README.md                   // README
├── README.zh-CN.md             // README.zh-CN
└── docs                        // Docsify Initialize the directory
    ├── _coverpage.md           // Cover page configuration
    ├── _sidebar.md             // Sidebar configuration file
    ├── .nojekyll               // Used to prevent GitHub Pages from ignoring files that start with an underscore
    ├── index.html              // Entry file, also a configuration file
    ├── README.md               // Render homepage content
    ├── advanced                // Articles - Advanced Front End
    ├── base                    // Articles - Front End Basics
    ├── book                    // Articles - Books
    ├── es6                     // Articles - ES6 basics
    ├── interesting             // Articles - interesting topics
    └── vuejs                   // Articles - Vue.js related

```

## Local development preview

```
cd docs/
docsify serve ./ -o
```

## Deploy

This project is deployed on `GitHub Pages`. GitHub Pages supports reading files from the project's `docs/` folder.
