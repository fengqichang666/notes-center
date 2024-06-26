# `axios`封装

###  `js`

#### 创建 `Axios `实例

在项目中创建一个 `axios.js` 文件，用于封装 `Axios` 实例和配置。

```js
// axios.js
import axios from 'axios';

// 创建 Axios 实例
const instance = axios.create({
  baseURL: 'https://api.example.com', // 基础URL，可以根据需要进行修改
  timeout: 10000, // 请求超时时间
  headers: {
    'Content-Type': 'application/json',
  },
});

// 请求拦截器
instance.interceptors.request.use(
  config => {
    // 在发送请求之前做些什么，例如添加 token
    const token = localStorage.getItem('token');
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  },
  error => {
    // 对请求错误做些什么
    return Promise.reject(error);
  }
);

// 响应拦截器
instance.interceptors.response.use(
  response => {
    // 对响应数据做些什么
    return response.data;
  },
  error => {
    // 对响应错误做些什么
    if (error.response) {
      // 请求已发出，但服务器响应的状态码不在 2xx 范围内
      switch (error.response.status) {
        case 401:
          // 未授权，处理登录逻辑
          console.error('Unauthorized, please login again.');
          break;
        case 403:
          // 禁止访问，处理权限逻辑
          console.error('Forbidden, you do not have permission to access this resource.');
          break;
        case 404:
          // 资源未找到
          console.error('Resource not found.');
          break;
        default:
          console.error('An error occurred:', error.response.data.message);
      }
    } else {
      // 请求未发出或其他错误
      console.error('An error occurred:', error.message);
    }
    return Promise.reject(error);
  }
);

export default instance;
```

#### 封装具体的请求方法

在 `api.js` 文件中封装具体的请求方法，如 `get`、`post`、`put`、`delete` 等。

```js
// api.js
import request from './axios';

// 封装 GET 请求
export const get = (url, config = {}) => {
  return request('get', url, null, config);
};

// 封装 POST 请求
export const post = (url, data, config = {}) => {
  return request('post', url, data, config);
};

// 封装 PUT 请求
export const put = (url, data, config = {}) => {
  return request('put', url, data, config);
};

// 封装 DELETE 请求
export const del = (url, config = {}) => { // 使用 del 作为函数名，因为 delete 是保留字
  return request('delete', url, null, config);
};

// 示例 API 请求方法
export const getUser = (userId) => {
  return get(`/users/${userId}`);
};

export const createUser = (userData) => {
  return post('/users', userData);
};

export const updateUser = (userId, userData) => {
  return put(`/users/${userId}`, userData);
};

export const deleteUser = (userId) => {
  return del(`/users/${userId}`);
};
```

### `ts`

```
npm install axios
npm install --save-dev @types/axios
```

```js
// axios.ts
import axios, { AxiosInstance, AxiosRequestConfig, AxiosResponse } from 'axios';

// 创建 Axios 实例
const instance: AxiosInstance = axios.create({
  baseURL: 'https://api.example.com', // 基础URL，可以根据需要进行修改
  timeout: 10000, // 请求超时时间
  headers: {
    'Content-Type': 'application/json',
  },
});

// 请求拦截器
instance.interceptors.request.use(
  (config: AxiosRequestConfig) => {
    // 在发送请求之前做些什么，例如添加 token
    const token = localStorage.getItem('token');
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  },
  (error) => {
    // 对请求错误做些什么
    return Promise.reject(error);
  }
);

// 响应拦截器
instance.interceptors.response.use(
  (response: AxiosResponse) => {
    // 对响应数据做些什么
    return response.data;
  },
  (error) => {
    // 对响应错误做些什么
    if (error.response) {
      // 请求已发出，但服务器响应的状态码不在 2xx 范围内
      switch (error.response.status) {
        case 401:
          // 未授权，处理登录逻辑
          console.error('Unauthorized, please login again.');
          break;
        case 403:
          // 禁止访问，处理权限逻辑
          console.error('Forbidden, you do not have permission to access this resource.');
          break;
        case 404:
          // 资源未找到
          console.error('Resource not found.');
          break;
        default:
          console.error('An error occurred:', error.response.data.message);
      }
    } else {
      // 请求未发出或其他错误
      console.error('An error occurred:', error.message);
    }
    return Promise.reject(error);
  }
);

// 通用请求方法
const request = <T>(method: string, url: string, data?: any, config?: AxiosRequestConfig): Promise<T> => {
  return instance({
    method,
    url,
    data,
    ...config,
  });
};

export default request;
```

```js
// api.ts
import request from './axios';

// 封装 GET 请求
export const get = <T>(url: string, config?: AxiosRequestConfig): Promise<T> => {
  return request<T>('get', url, null, config);
};

// 封装 POST 请求
export const post = <T>(url: string, data: any, config?: AxiosRequestConfig): Promise<T> => {
  return request<T>('post', url, data, config);
};

// 封装 PUT 请求
export const put = <T>(url: string, data: any, config?: AxiosRequestConfig): Promise<T> => {
  return request<T>('put', url, data, config);
};

// 封装 DELETE 请求
export const del = <T>(url: string, config?: AxiosRequestConfig): Promise<T> => { // 使用 del 作为函数名，因为 delete 是保留字
  return request<T>('delete', url, null, config);
};

// 示例 API 请求方法
export const getUser = (userId: number) => {
  return get<{ id: number, name: string, email: string }>(`/users/${userId}`);
};

export const createUser = (userData: { name: string, email: string }) => {
  return post<{ id: number, name: string, email: string }>('/users', userData);
};

export const updateUser = (userId: number, userData: { name: string, email: string }) => {
  return put<{ id: number, name: string, email: string }>(`/users/${userId}`, userData);
};

export const deleteUser = (userId: number) => {
  return del<void>(`/users/${userId}`);
};
```

