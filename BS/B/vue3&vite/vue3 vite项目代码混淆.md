# vue3 vite项目代码进行混淆加密

场景： 本来用的是vben admin框架  vite版本是2  试了很多代码混淆工具  都不行 安装插件的时候会有提示 vite版本要大于3

## 第一步 升级vite版本至最新

### package.json更改

| 依赖                                                 | 原有版本 | 升级后版本          |
| ---------------------------------------------------- | -------- | ------------------- |
| terser（新增）                                       | 无       | "terser": "^5.20.0" |
| @vitejs/plugin-legacy                                | ^1.8.1   | ^3.0.2              |
| @vitejs/plugin-vue                                   | ^2.3.1   | ^4.3.4              |
| rollup                                               | ^2.70.2  | ^3.29.2             |
| rollup-plugin-visualizer                             | ^5.6.0   | ^5.9.2              |
| vite                                                 | ^2.9.5   | ^4.4.9              |
| vite-plugin-purge-icons（应该不是必须升级的）        | ^0.8.1   | ^0.9.2              |
| vite-plugin-pwa                                      | ^0.11.13 | ^0.16.4             |
| vite-plugin-theme -->@kirklin/vite-plugin-vben-theme | ^0.8.6   | ^0.1.2              |
| vue-eslint-parser                                    | ^8.3.0   | ^9.3.1              |
| resolutions对象下：                                  |          |                     |
| rollup                                               | ^2.56.3  | ^3.29.2             |

### 全局搜索使用vite-plugin-theme的地方  替换为@kirklin/vite-plugin-vben-theme

### `import.meta.globEager` 已经弃用，请使用 `import.meta.glob('*', { eager: true })` 来代替。



## 引入插件vite-plugin-obfuscator

> tips：其他插件都不能用，可能是本人用的不对

添加完依赖后

```
import { viteObfuscateFile } from 'vite-plugin-obfuscator';

vitePlugins.push(
      viteObfuscateFile({
        compact: true,
        controlFlowFlattening: false,
        deadCodeInjection: false,
        debugProtection: false,
        debugProtectionInterval: 0,
        disableConsoleOutput: false,
        identifierNamesGenerator: 'hexadecimal',
        log: false,
        numbersToExpressions: false,
        renameGlobals: false,
        selfDefending: false,
        simplify: true,
        splitStrings: false,
        stringArray: false, // 此处必须为false  不然样式会有问题
        stringArrayCallsTransform: false,
        stringArrayCallsTransformThreshold: 0.5,
        stringArrayEncoding: [],
        stringArrayIndexShift: true,
        stringArrayRotate: true,
        stringArrayShuffle: true,
        stringArrayWrappersCount: 1,
        stringArrayWrappersChainedCalls: true,
        stringArrayWrappersParametersMaxCount: 2,
        stringArrayWrappersType: 'variable',
        stringArrayThreshold: 0.75,
        unicodeEscapeSequence: false,
      }),
    );
```





