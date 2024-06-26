

# sha256+salt参数加密

```js
import sha256 from 'crypto-js/sha256';
import { getAppEnvConfig } from '/@/utils/env';
const { VITE_GLOB_API_SALT } = getAppEnvConfig();
export const setSha = (data) => {
  //  VITE_GLOB_API_SALT 一个特殊加密字符串
  const encrypt = sha256(JSON.stringify(data) + VITE_GLOB_API_SALT).toString(); //要加密的密码
  return encrypt;
};

```

axios请求拦截器requestInterceptors中，调用方法 传入请求的参数 ，返回值放在请求头`x-signature`(约定字段名)

