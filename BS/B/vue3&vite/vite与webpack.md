# vite与webpack

## webpack问题

公司的项目基本都是基于webpack构建，而我们都会对webpack构建项目进行一些构建速度上的优化，比如缓存等，但是开了缓存之后，开发环境还是会存在慢的问题，主要有两方面

> - 首次启动慢
> - HMR慢

首次启动慢的原因是因为webpack是一种bundler方案，需要根据entry找到所有依赖模版，并输出最终的bundle.js；

hmr慢是因为babel-loader做语法转换耗时过长,且需要重新生产bundle.js

所以针对上面的问题，首先我们可能会想到怎么降低语法转化时间，比如使用esbuild-loader来替换babel-loader，但是这只能减少部分语法转换时间，还是不能解决首次启动慢的问题，所以要从根本上解决开发环境首次启动慢的问题还得换一种思路，也就是现在vite提供的bundless思路，启动的时候，只进行预构建第三方依赖，实际浏览器访问的时候，在通过本地服务器实时转换每个请求的文件，达到缩短首次启动时间的目的

## 预构建

做了什么：

- 扫描入口文件，然后通过这些入口，扫描所有用到的依赖
- 将多个依赖进行打包
- 修改这些模块的引入路径 ↓

```
- import { createApp, defineCustomElement } from 'vue'
+ import { createApp, defineCustomElement } from '/node_modules/.vite/deps/vue.js?v=b92a21b7'
```

node_module 下会多了一个 `.vite` 文件，依赖预构建的产物会放在 `deps` 目录下

由于 import vue 这种模块引入方式，使用的是 Nodejs 特有的模块查找算法（到 node_modules 中取查找），浏览器无法使用，因此 Vite 会将 `vue` 替换成一个另一个路径，当浏览器解析到这行 import 语句时，会发送一个 `	/node_modules/.vite/deps/vue.js?v=b92a21b7`， Vite Server 会到该目录下，拿到 vue 预构建之后的产物代码。

### 为什么要预构建

> **CommonJS 和 UMD 兼容性:** 开发阶段中，Vite 的开发服务器将所有代码视为原生 ES 模块。因此，Vite 必须先将作为 CommonJS 或 UMD 发布的依赖项转换为 ESM。
>
> **性能：** Vite 将有许多内部模块的 ESM 依赖关系转换为单个模块，以提高后续页面加载性能

一些包将它们的 ES 模块构建作为许多单独的文件相互导入。例如，[`lodash-es` 有超过 600 个内置模块](https://unpkg.com/browse/lodash-es/)！当我们执行 `import { debounce } from 'lodash-es'` 时，浏览器同时发出 600 多个 HTTP 请求！尽管服务器在处理这些请求时没有问题，但大量的请求会在浏览器端造成网络拥塞，导致页面的加载速度相当慢。

通过预构建 `lodash-es` 成为一个模块，我们就只需要一个 HTTP 请求了！

## esbuild的打包速度为什么快

### 语言优势

webpack基于js实现，esbuild基于go语言实现。

js作为一门解释型语言，程序每次执行都需要先由解释器一边将源码翻译成机器语言，一边调度执行；而go是一种编译型语言，在编译阶段就已经将源码转为机器码，启动时只需要直接执行这些机器码即可。相当于，当esbuild解析我们的js代码文件时，node可能正忙于解析我们打包工具webpack的js代码。

### 多线程优势

go语言具有多线程运行能力，而js本质是一门单线程语言，虽然引入了WebWorker规范后，我们可以使用js在浏览器、Node中实现多线程操作，但是在查阅资料后webpack中并未使用webworker提供的多线程能力。反观esbuild，尽可能饱和地使用各个CPU核，特别是打包过程的解析、代码生成阶段的过程。

### 与ts-loader以及babel-loader对比

##### ts-loader

webpack的ts加载器，将ts编译成js。ts-loader在内部是调用了ts的官方编译器--tsc，所以ts-loader和tsc是共享tsconfig.json文件的

为了性能的提升，我们一般会设置transpileOnly: true，只做语言的转换，而不做类型检查。esbuild同样完全抛弃了ts的类型检查，只做代码转换。

##### babel-loader（搬运官网）

在webpack中使用其将es6+高版本js代码转译为es5低版本，此过程可能需要经过多次数据转换：

- Webpack 读入源码，此时为字符串形式
- Babel 解析源码，转换为 AST 形式
- Babel 将源码 AST 转换为低版本 AST
- Babel 将低版本 AST generate 为低版本源码，字符串形式
- Webpack 解析低版本源码
- Webpack 将多个模块打包成最终产物

源码需要经历 `string => AST => AST => string => AST => string` ，在字符串与 AST 之间反复横跳。

esbuild最大的不同就是能够在多个编译阶段共用相似的AST结构，尽可能减少字符串到AST的结构转换

### esbuild 的缺点

esbuild 只能将代码转成 es6。

为了保证 esbuild 的编译效率，esbuild 没有提供 AST 的操作能力。所以一些通过 AST 处理代码的 babel-plugin 没有很好的方法过渡到 esbuild 中（如babel-plugin-import）。so，如果你的项目使用了 babel-plugin-import, 或者一些自定义的 babel-plugin 。在目前来看是没有很好的迁移方案的。

### 总结

1. js是单线程串行，esbuild是新开一个进程，然后多线程并行，充分发挥多核优势
2. go是纯机器码，肯定要比JIT(即时编译)快
3. 不使用 AST，优化了构建流程。

## 为什么说vite快？为什么说vite慢？

vite项目的启动确实比webpack快，但如果某个界面是首次进入，且依赖比较多/比较复杂的话，那就会比较慢了

vite的快：命令行启动快，vite启动时并不会像webpack一样对所有代码进行编译/打包/压缩。官网的说法是，vite通过在一开始将应用中的模块区分为 依赖 和 源码 两类，改进了开发服务器启动时间，以 原生 ESM 方式提供源码，让浏览器接管了打包程序的部分工作，只需要在浏览器请求源码时进行转换并按需提供源码（运行时进行依赖分析，动态打包，动态引入）。而webpack需要在内存中编译、打包、压缩。（`这里的启动是指命令行启动完毕，不是指启动完之后首页加载完毕`）

vite的慢：加载依赖项很多，很复杂的页面时，页面打开慢。需要一次性请求太多的资源，而且这些资源有些并不是浏览器可以直接运行的，vite还需要动态的解析（**一系列的动态分析/动态资源引入/动态编译**），然后一些需要打包再返回给浏览器。

vite启动快的原理，也导致了他在加载依赖项很多，很复杂的页面时，页面打开慢。因为他要进行**一系列的动态分析/动态资源引入/动态编译**。

总结：

- 启动的时候只做第三方模块的预构建，且使用esbuild来进行预构建，速度是毫秒级
- 访问页面的时候，在处理路由对应的模块，同样使用esbuild来做转换，所以速度还是非常快

## 什么是依赖预编译	

会**在 DevServer 启动前**对需要预编译的依赖进行编译，然后在分析模块的导入（import）时会动态地**应用编译过的依赖**。

- 默认情况下，Vite 会将 package.json 中生产依赖 `dependencies` 的部分启用依赖预编译，即会先对该依赖进行编译，然后将编译后的文件缓存在内存中（node_modules/.vite 文件下），在启动 DevServer 时直接请求该缓存内容。
- 在 vite.config.js 文件中配置 `optimizeDeps` 选项可以选择**需要或不需要**进行预编译的依赖的名称，Vite 则会根据该选项来确定是否对该依赖进行预编译。
- 在启动时添加 `--force` options，可以用来**强制重新**进行依赖预编译。

## 解决方案

通过添加类似如下的配置，让vite在启动之初就对某些资源进行预打包，尽量避免后续的动态打包，示例配置如下
`vite.config.ts`

```
{
   optimizeDeps: {
      include: [
        'vue',
        'map-factory',
        'element-plus/es',
        'element-plus/es/components/form/style/index',
        'element-plus/es/components/radio-group/style/index',
        'element-plus/es/components/radio/style/index',
        'element-plus/es/components/checkbox/style/index',
        'element-plus/es/components/checkbox-group/style/index',
        'element-plus/es/components/switch/style/index',
        'element-plus/es/components/time-picker/style/index',
        'element-plus/es/components/date-picker/style/index',
        'element-plus/es/components/col/style/index',
        'element-plus/es/components/form-item/style/index',
        'element-plus/es/components/alert/style/index',
        'element-plus/es/components/breadcrumb/style/index',
        'element-plus/es/components/select/style/index',
        'element-plus/es/components/input/style/index',
        'element-plus/es/components/breadcrumb-item/style/index',
        'element-plus/es/components/tag/style/index',
        'element-plus/es/components/pagination/style/index',
        'element-plus/es/components/table/style/index',
        'element-plus/es/components/table-column/style/index',
        'element-plus/es/components/card/style/index',
        'element-plus/es/components/row/style/index',
        'element-plus/es/components/button/style/index',
        'element-plus/es/components/menu/style/index',
        'element-plus/es/components/sub-menu/style/index',
        'element-plus/es/components/menu-item/style/index',
        'element-plus/es/components/option/style/index',
        '@element-plus/icons-vue',
        'pinia',
        'axios',
        'vue-request',
        'vue-router',
        '@vueuse/core',
      ],
    }
}

```

### 插件[vite-plugin-optimize-persist](https://www.npmjs.com/package/vite-plugin-optimize-persist)

```
npm i -D vite-plugin-optimize-persist vite-plugin-package-config
```

`vite.config.ts` 中增加配置

```
// vite.config.ts
import OptimizationPersist from 'vite-plugin-optimize-persist'
import PkgConfig from 'vite-plugin-package-config'

export default {
  plugins: [
    PkgConfig(),
    OptimizationPersist()
  ]
}
```

首次加载的时候，依然会很慢，这个是正常现象，因为这个插件, 加快vite载入界面速度的原理, 也和上面说的一样，而第一次，这个插件也没法知道，哪些依赖需要预构建，他只是在vite动态引入资源的时候，将这些资源都记录下来，自动写入了package.json中，当再次启动项目的时候，插件会读取之前他写入在package.json中的数据，并告知vite，这样vite就能对这些资源进行预构建了，也就能加快进入界面的速度了，但相应的启动速度就会比原来稍微慢一点

## vite的内部核心流程图
