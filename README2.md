
## 前端prerender-spa-plugin  单页面渲染

  #### 1. 安装

  ###### npm install prerender-spa-plugin --s

  #### 2. vue.config.js中引用
  ```
  1.
    const PrerenderSPAPlugin = require('prerender-spa-plugin');
      const Renderer = PrerenderSPAPlugin.PuppeteerRenderer;
      const path = require('path');
      module.exports = {
        configureWebpack: config => {
          if (process.env.NODE_ENV !== 'production') return;
          return {
            plugins: [
              new PrerenderSPAPlugin({
                // 生成文件的路径，也可以与webpakc打包的一致。
                // 下面这句话非常重要！！！
                // 这个目录只能有一级，如果目录层次大于一级，在生成的时候不会有任何错误提示，在预渲染的时候只会卡着不动。
                staticDir: path.join(__dirname,'dist'),
                // 对应自己的路由文件，比如a有参数，就需要写成 /a/param1。
                // routes: ['/', '/product','/about'],
                routes: ['/','/about'],
                // 这个很重要，如果没有配置这段，也不会进行预编译
                renderer: new Renderer({
                  inject: {
                    foo: 'bar'
                  },
                  headless: false,
                  // 在 main.js 中 document.dispatchEvent(new Event('render-event'))，两者的事件名称要对应上。
                  renderAfterDocumentEvent: 'render-event'
                })
              }),
            ],
          };
        },
      }
  ```
  #### 3. mian.js中引用
  ```
    new Vue({
      router,
      render: h => h(App),
      mounted () {
        document.dispatchEvent(new Event('render-event'))
      }
    }).$mount('#app')
  ```



## 前端prerender-spa-plugin  多页面渲染

  #### 1. 安装

  ###### npm install prerender-spa-plugin --s

  #### 2. vue.config.js中引用
  ```
    const PrerenderSPAPlugin = require('prerender-spa-plugin');
    const Renderer = PrerenderSPAPlugin.PuppeteerRenderer;
    const path = require('path');
    module.exports = {
        configureWebpack: config => {
            if (process.env.NODE_ENV !== 'production') return;
            return {
                plugins: [
                    new PrerenderSPAPlugin({
                        // 生成文件的路径，也可以与webpakc打包的一致。
                        // 下面这句话非常重要！！！
                        // 这个目录只能有一级，如果目录层次大于一级，在生成的时候不会有任何错误提示，在预渲染的时候只会卡着不动。
                        staticDir: path.join(__dirname,'dist'),
                        // 对应自己的路由文件，比如a有参数，就需要写成 /a/param1。
                        // routes: ['/', '/product','/about'],
                        routes: ['/','/about'],
                        // 这个很重要，如果没有配置这段，也不会进行预编译
                        renderer: new Renderer({
                            inject: {
                                foo: 'bar'
                            },
                            headless: false,
                            // 在 main.js 中 document.dispatchEvent(new Event('render-event'))，两者的事件名称要对应上。
                            renderAfterDocumentEvent: 'render-event'
                        })
                    }),
                ],
            };
        },
        pages: {
            ui: {
              // page 的入口
              entry: "./src/views/index/mian.js",
              // 模板来源
              template: "public/index.html",
              // 在 dist/index.html 的输出
              filename: "index.html",
              // 当使用 title 选项时，
              // template 中的 title 标签需要是 <title><%= htmlWebpackPlugin.options.title %></title>
              title: "Index Page",
              // 在这个页面中包含的块，默认情况下会包含
              // 提取出来的通用 chunk 和 vendor chunk。
              chunks: ["chunk-vendors", "chunk-common", "ui"]
            },
            hh: {
              // page 的入口
              entry: "./src/views/intro/mian.js",
              // 模板来源
              template: "public/intro.html",
              // 在 dist/index.html 的输出
              filename: "intro.html",
              // 当使用 title 选项时，
              // template 中的 title 标签需要是 <title><%= htmlWebpackPlugin.options.title %></title>
              title: "Index Page",
              // 在这个页面中包含的块，默认情况下会包含
              // 提取出来的通用 chunk 和 vendor chunk。
              chunks: ["chunk-vendors", "chunk-common", "hh"]
            },
            // 当使用只有入口的字符串格式时，
            // 模板会被推导为 `public/subpage.html`
            // 并且如果找不到的话，就回退到 `public/index.html`。
            // 输出文件名会被推导为 `subpage.html`。
            // subpage: 'src/subpage/main.js'
        },
        publicPath: './'
    }
  ```
  #### 3. main.js中的引用
  ```
    new Vue({
      router,
      render: h => h(App),
      mounted () {
        document.dispatchEvent(new Event('render-event'))
      }
    }).$mount('#app')
  ```

  #### 4. public(多页面这边只写两个页面)
  ```
    |-public
    |  |- index.html
    |  |- intro.html
  ```

  #### 5.scr 中修改
  ```
    |-- src/
    | |-- view/
    | | |-- index/
    | | | |-- App.vue
    | | | |-- router.js
    | | | |-- index.vue
    | | | |-- main.js
    | | |
    | | |-- intro/
    | | | |-- App.vue
    | | | |-- router.js
    | | | |-- index.vue
    | | | |-- main.js
  ```