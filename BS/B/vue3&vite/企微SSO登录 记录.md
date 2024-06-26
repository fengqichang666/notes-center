# 企微SSO登录 记录

文档：[Web登录组件 - 文档 - 企业微信开发者中心 (qq.com)](https://developer.work.weixin.qq.com/document/path/98152)

使用方式：**构造企业微信登录链接**

在新窗口中打开企业微信登录页面，用户使用企业微信登录授权后通过将携带 `auth code` 跳转至指定的 `redirect_uri`

### 链接格式

开发者需要构造如下的链接来获取 `code` 参数

> `https://login.work.weixin.qq.com/wwlogin/sso/login?login_type=LOGIN_TYPE&appid=APPID&redirect_uri=REDIRECT_URI&state=STATE`

| 参数名         | 类型   | 必填 | 说明                                                         |
| -------------- | ------ | ---- | ------------------------------------------------------------ |
| `login_type`   | string | 是   | 登录类型。 `ServiceApp`：服务商登录；`CorpApp`：企业自建/代开发应用登录。 |
| `appid`        | string | 是   | 登录类型为企业自建应用/服务商代开发应用时填企业 `CorpID`，第三方登录时填[登录授权 SuiteID](https://developer.work.weixin.qq.com/document/path/98152#45846/开启网页授权登录) |
| `agentid`      | string | 否   | 企业自建应用/服务商代开发应用 `AgentID`，当`login_type=CorpApp`时填写 |
| `redirect_uri` | string | 是   | 登录成功重定向 `url`，需进行 `URLEncode` 注意域名必须配置为可信域名，详见下文的“redirect_uri域名说明” |
| `state`        | string | 否   | 登录 `state` 用于保持请求和回调的状态，授权请求后原样带回给企业。该参数可用于防止`CSRF` 攻击（跨站请求伪造攻击），建议带上该参数，可设置为简单的随机数加 `session` 进行校验 需进行 `URLEncode` |
| `lang`         | string | 否   | 语言类型。`zh`：中文；`en`：英文。                           |

**`redirect_uri`域名说明**
redirect_uri的域名必须配置为可信域名，不同应用类型的配置方法不同，具体如下

| 应用类型   | 域名配置方法                         |
| ---------- | ------------------------------------ |
| 自建应用   | OAuth可信域名或者Web网页授权回调域名 |
| 代开发应用 | OAuth可信域名或者Web网页授权回调域名 |
| 第三方应用 | “登录授权”的可信域名                 |

**返回说明**
用户允许授权后，将会重定向到 `redirect_uri` 的网址上，并且带上 `code` 和 `state` 参数

> `redirect_uri?code=CODE&state=STATE`

### **示例：自建应用**

```
企业CorpID：WWCorpId
开启授权登录的应用ID：1000000
登录跳转链接：http://work.weixin.qq.com
state设置为：WWLogin
需要配置应用授权回调域名为：work.weixin.qq.com

根据URL规范，将上述参数分别进行UrlEncode，得到拼接的登录链接为：
https://login.work.weixin.qq.com/wwlogin/sso/login?login_type=CorpApp&appid=WWCorpId&agentid=1000000&redirect_uri=http%3A%2F%2Fwork.weixin.qq.com&state=WWLogin
```

### 实际应用

点击`sso登录`按钮

```js
  const toSsoLogin = async () => {
  	//点击sso登录 发接口获取到太古提供的链接  login_type appid是太古内部的  因此要访问太古的链接
  	//data   https://zfttest-login.app.swirecocacola.com/oidc/auth?client_id=1345465&scope=openid profile email phone 		extended_fields external_id users:*:select&state=bme8myzy4wec&nonce=zm62na3jji7j&response_type=code&redirect_uri=https://zft-portal-	qnr.app.swiretest.com/zft/ssoCallback
    let hrefUri: any = await SSOLoginApi.getSSOLoginUrl();、
    const RE = /&state=.*?&/;
    let query = userStore.getQuery; //原本url的query数据放入state带过去
    query.redirect = route.query?.redirect;
    query = JSON.stringify(query);
    if (RE.test(hrefUri)) {
      hrefUri = hrefUri.replace(RE, `&state=${query}&`);
    }
    window.location.href = hrefUri;
  };
```

回调页面`ssoCallback`(页面的路由自行配置)

```js
//从url中获取code
const code = computed(() => route.query.code); 
const handleLogin = async () => {
    if (code.value) {
      try {
        spinning.value = true;
        const userInfo: any = await userStore.ssoLogin({ ssoCode: code.value });
         /***
         *
         * 登录成功逻辑省略
         */
      } catch (error) {
        createErrorModal({
          title: t('sys.api.errorTip'),
          content: (error as unknown as Error).message || t('sys.api.networkExceptionMsg'),
          getContainer: () => document.body.querySelector(`.${prefixCls}`) || document.body,
          onOk() {
            router.replace('/login');
          },
        });
      } finally {
        spinning.value = false;
      }
    }
  };
  handleLogin();
```

