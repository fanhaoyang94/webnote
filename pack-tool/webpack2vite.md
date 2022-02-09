# Webpack To Vite

#### 入口文件

1. `index.html` 需要在根目录下，在开发期间 Vite 是一个服务器，而 index.html 是该 Vite 项目的入口文件 [[参考官网]](https://cn.vitejs.dev/guide/#index-html-and-project-root)
2. `index.html` 需要引入 `<script type="module" src="./src/main.js">` 路径为 webpack 的入口文件

#### .vue 扩展名

1. 在 `Vite` 项目中默认不可忽略 `.vue` 扩展名，否则会导入失败作者设计时规定，为了避免影响 IDE 和类型支持 [[参考官网]](https://cn.vitejs.dev/config/#resolve-extensions)
2. 可以配置但是不建议

```
resolve{
  extensions: ['.mjs', '.js', '.ts', '.jsx', '.tsx', '.json', '.vue']
}
```

#### scss

1. 在 `Vite` 项目中对于 `scss` 的开发依赖仅仅需要下载 `scss` 即可，然后按照文档配置

#### webpack 转 vite 工具 wp2vite

```
npx wp2vite
```

等待转换完成后

```
npm install && npm run vite-start
```

##### 注意：

1. 此工具转换过程依赖 `vue.config.js` 文件，但是 `vue-cli` 创建项目时样式的配置不体现所以需要注意转换后手动配置
2. `alias` 别名转换后路径需要注意修改 `src` 前不需要前缀

```
  let alias = {
      '@router': path.resolve(__dirname, 'src/router'),
    }
```

3. `outPut` 目录转换失败需要手动修正

## 参考链接

[1] [Webpack to Vite， 为开发提速](https://blog.csdn.net/xgangzai/article/details/115911688)  
[2] [Vue3.0 项目从 Webpack 改造成 Vite](https://www.jianshu.com/p/9f42c72c1655)
