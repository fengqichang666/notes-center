# `GitHub Pages`+`Actions`自动部署`vite`项目 ,`msw(mock)`数据生产环境使用

## 项目代码更改

`main.ts`

```typescript
// 判断环境  读取homepage（服务器上存放的位置 应用程序的主目录）
import { StartOptions } from 'msw/browser';
import packageJson from '../package.json';

const { MODE } = import.meta.env;
let options: StartOptions = { onUnhandledRequest: 'bypass' };
if (MODE != 'development') {
    options.serviceWorker = { url: `${packageJson.homepage}/mockServiceWorker.js` };
}
worker.start(options);
```

`package.json`

```json
  // 增加
  "homepage": "/react-admin", // github.io下react-admin项目的GitHub Pages
```

`vite.config.ts`

```typescript
// 增加
base: command === 'build' ? '/react-admin/' : '/',  // 开发或生产环境服务的公共基础路径
```

`.env`文件

```typescript
如果axios中配置了baseURL  须保持development中与production中配置的BASE_API相同
const axiosInstance = axios.create({
    baseURL: import.meta.env.VITE_APP_BASE_API,
    timeout: 20 * 1000,
    headers: { 'Content-Type': 'application/json;charset=utf-8' }
});
```

`.github/workflows/ci.yml`文件新建

```yaml
name: Build and Deploy
on:
  push:
    branches:
      - master
permissions:
  contents: write
jobs:
  build-and-deploy:
    concurrency: ci-${{ github.ref }} # Recommended if you intend to make multiple deployments in quick succession.
    runs-on: ubuntu-latest
    steps:
      - name: Checkout 🛎️
        uses: actions/checkout@v4

      - name: Install and Build 🔧 # This example project is built using npm and outputs the result to the 'build' folder. Replace with the commands required to build your project, or remove this step entirely if your site is pre-built.
        run: |
          npm install
          npm run build

      - name: Deploy 🚀
        uses: JamesIves/github-pages-deploy-action@v4
        with:
          folder: dist # The folder the action should deploy.
          CLEAN: true

```

## `github`项目配置

**项目Settings的  Pages 选项    `Build and deployment` 下`Source`选择 `Deploy from a branch` 、`Branch`选`gh-pages`   ` /root`**

**代码push以后 会自动触发项目的Actions  把代码打包到`gh-pages`分支  执行完毕打开https://fengqichang666.github.io/react-admin/ 即可看到最新页面**

