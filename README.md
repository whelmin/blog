**English** | [中文](./README.zh-CN.md)

<h1 align="center">Hui Wen's Blog</h1>

## Dependencies

* [docsify-cli](https://www.npmjs.com/package/docsify-cli)
* [Disqus](https://disqus.com/)

This project depends on docsify-cli scaffold to quickly create a project structure, preview documents locally, use Disqus add comments.

## Tree of project

```
.
├── .gitignore                  // git ignore config
├── README.md                   // README
├── README.zh-CN.md             // README.zh-CN
└── docs                        // Docsify Initialize the directory
    ├── _coverpage.md           // Cover page configuration
    ├── _navbar.md              // Navbar configuration file
    ├── _sidebar.md             // Sidebar configuration file 
    ├── .nojekyll               // Used to prevent GitHub Pages from ignoring files that start with an underscore
    ├── index.html              // Entry file, also a configuration file
    ├── README.md               // Render homepage content
    ├── advanced                // Articles - Advanced Front End
    ├── base                    // Articles - Front End Basics
    ├── book                    // Articles - Books
    ├── es6                     // Articles - ES6 basics
    ├── interesting             // Articles - interesting topics
    ├── vue2                    // Articles - Vue.js 2
    └── ...                     // Articles - xxx

```

## Local development preview

```
cd docs/
docsify serve ./ -o
```

## Deploy

This project is deployed on `GitHub Pages`. GitHub Pages supports reading files from the project's `docs/` folder.
