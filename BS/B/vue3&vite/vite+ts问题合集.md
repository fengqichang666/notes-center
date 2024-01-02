## vite + vue3 + ts,创建项目时，找不到模块“../views/HomeView.vue”或其相应的类型声明。ts(2307)

原因：ts文件无法识别.vue文件

解决方案：在src 目录下创建 .d.ts结尾的文件,可以直接加在env.d.ts中

```typescript
declare module "*.vue" {
    import Vue from "@/vue";
    export default Vue;
}
```

如果tsconfig.json文件include选项未包含`src/**/*.d.ts`,需自行添加

## 配置eslint+prettier

1.安装依赖

```
npm i @typescript-eslint/eslint-plugin @typescript-eslint/parser eslint eslint-config-prettier eslint-config-standard eslint-plugin-import eslint-plugin-node eslint-plugin-prettier eslint-plugin-promise eslint-plugin-vue prettier prettier-eslint vue-eslint-parser -D
```

2.当前项目 .vscode文件夹 settings.json文件

```json
{
  "editor.formatOnSave": true,
  "editor.tabSize": 4,
  "editor.indentSize": "tabSize",
  "editor.detectIndentation": true,
  "editor.insertSpaces": false,
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "eslint.format.enable": true,
  "eslint.validate": [
    "vue",
    "html",
    "javascript",
    "typescript",
    "javascriptreact",
    "typescriptreact",
    "less",
    "css",
    "scss",
    "json",
    "jsonc"
  ],
  "[vue]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[html]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[javascript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[typescript]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[javascriptreact]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[typescriptreact]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[less]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[css]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[scss]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[json]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },
  "[jsonc]": {
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  }
}

```

3.prettierrc.json

```json
{
	"$schema": "https://json.schemastore.org/prettierrc",
	"semi": false,
	"singleQuote": true,
	"printWidth": 100,
	"trailingComma": "none",
	"arrowParens": "avoid",
	"vueIndentScriptAndStyle": true,
     "useTabs": true,
	"tabWidth": 4
}
```

4.eslintrc.cjs

```js
/* eslint-env node */
require('@rushstack/eslint-patch/modern-module-resolution')

module.exports = {
  root: true,
  extends: [
    'plugin:vue/vue3-essential',
    'eslint:recommended',
    '@vue/eslint-config-typescript',
    '@vue/eslint-config-prettier/skip-formatting'
  ],
  rules: {
      //使用tab缩进 script标签内容缩进1tab
     'vue/script-indent': [
			'error',
			'tab',
			{
				// script标签缩进设置
				baseIndent: 1
			}
		],
      //关闭命名规则校验 解决Component name "index" should always be multi-word.eslint报错 
      'vue/multi-word-component-names': 'off',
      // 解决html文件末尾ESlint(vue/comment-directive)报错提示clear
      'vue/comment-directive': 'off'
  },
  parserOptions: {
     //解决Parsing error: Unexpected token : eslint报错
    parser: '@typescript-eslint/parser',
    ecmaVersion: 'latest'
  }
}
```



****

## 模块 ""element-plus"" 没有导出的成员 "FormRules"。你是想改用 "import FormRules from "element-plus"" 吗?

原因： TS 无法主动发现模块，如果找不到模块，则需要在此使用 declare module 进行配置

env.d.ts文件添加一行

```
declare module 'element-plus'
```

## 不能将命名空间“FormRules”用作类型。

```
方案1
const rules = reactive<InstanceType<typeof FormRules>>({
})

方案2
import type { ElForm } from 'element-plus'
type FormRules = InstanceType<typeof ElForm>
const rules = reactive<FormRules>({})
方案3 仅针对ref获取组件实例
如果组件的具体类型无法获得，或者你并不关心组件的具体类型，那么可以使用 ComponentPublicInstance。这只会包含所有组件都共享的属性，比如 $el。 
import { type ComponentPublicInstance, ref } from 'vue'
const ruleFormRef = ref<ComponentPublicInstance>()
```

## 监听子组件生命周期

```
vue2写法  @hook:mounted = "tabMounted"
vue3写法  @vnode-mounted= "tabMounted"
```

