---
title: 前端如何配合服务端进行 CORS 跨域
date: 2017-05-02 15:44:25
tags: ajax cors
---

> 最近正在用 vue.js + vuex + axios 开发一个后台管理的项目。由于服务端的接口地址与 web 的访问不在同一个域里面，所以必须要处理跨域的问题，通过调研跨域的方法，最终决定使用目前比较流行的 CORS 来处理跨域 ，以下介绍在使用 cors 的时候遇到的那些坑，以及处理方法。

现在所有的浏览器基本上都支持 CORS（IE需要10以上），所以只需要服务器实现 CORS 的接口，就可以跨域通讯了。服务端基本的配置如下：

```
"Access-Control-Allow-Origin" => $host_name,
"Access-Control-Allow-Headers" => "x-token,x-uid,x-token-check,x-requested-with,content-type,Host,auth-token,Authorization",
```
服务端配置完以后，前端在发起请求，就会变成两个。首先会向服务器发一个预检请求。

{% asset_img option-request.png option-request %}

从图中可以看到请求的方法是：OTPTION。这个请求只要是用于检查
Access-Control-Allow-Origin
Access-Control-Request-Headers 这两个字段，确认允许跨域请求以后，才正式发出我们想要的请求

{% asset_img myself-request.png myself-request %}

完成这一步，CORS 请求基本上就成功了。

但是请别高兴的太早，CORS 请求默认是不发送 `Cookies` 和 `HTTP` 认证信息的。而服务端需要

```
”Access-Control-Expose-Headers” => ‘auth-token’
```

通过 `Cookies` 里面的信息来判断的依据，所以这时候，我们就要这是 `withCredentials` 属性。

一方面需要服务端同意发送 `Cookies`，指向 `Access-Control-Allow-Credentials` 字段。

```
"Access-Control-Allow-Credentials" => 'true',

```

另一方面，前端也必须在 `ajax` 请求的设置 `withCredentials` 属性。（这里是用的是 `Axios` 的配置）

```
const axiosInstance = axios.create({
  withCredentials: true
})
```

服务端和前端同时设置完 `withCredentials` 属性之后，就可以自动发送 `Cookies` 。

需要注意的是，在 `Header` 里面有些自定的字段，前端没有办法获取到。例如用于自定义存放 `token` 的字段，我们采用了 `auth-token` ，默认情况下是不允许获取的。这时候服务端需要设置 `Access-Control-Expose-Headers` 字段，允许请求中携带 `auth-token`。

```
"Access-Control-Expose-Headers" => 'auth-token'

```

完成以上步骤，就大功告成了，以下贴出服务端与前端设置，以供参考：

** PHP 设置 CORS 头部 **

```php
function get_cross_headers(){
    $host_name = isset($_SERVER['HTTP_ORIGIN']) ? $_SERVER['HTTP_ORIGIN'] : "*";
    $headers = [
        "Access-Control-Allow-Origin" => $host_name,
        "Access-Control-Allow-Credentials" => 'true',
        "Access-Control-Allow-Headers" => "x-token,x-uid,x-token-check,x-requested-with,content-type,Host,auth-token,Authorization",
        "Access-Control-Expose-Headers" => 'auth-token'
    ];
    return $headers;
}
```

** Axios 配置 **

```javascript
const axiosInstance = axios.create({
  withCredentials: true
})
axiosInstance.interceptors.request.use(config => {
  const token = ls.get('token') //从 localstorage 中获取token
  if (token) {
    config.headers['auth-token'] = token
  }
  return config
}, function (error) {
  return Promise.reject(error)
})
axiosInstance.interceptors.response.use(response => {
  const token = response.headers['auth-token']
  token && ls.set('token', token) //将 token 存入 localstorage
  return response.data
}, function (error) {
  return Promise.reject(error)
})
```

** Ref **

- [跨域资源共享 CORS 详解](http://www.ruanyifeng.com/blog/2016/04/cors.html)
- [HTTP访问控制（CORS）](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Access_control_CORS)