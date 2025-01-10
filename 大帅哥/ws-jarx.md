## Springboot & cxf 实现webservice客户端调用案例

#### 引入依赖包

```
implementation 'org.springframework.boot:spring-boot-starter-web-services'

implementation 'org.apache.cxf:cxf-spring-boot-starter-jaxws:3.2.1'
```

#### 代码示例
```
@Service
@Slf4j
public class WsInvokeClient {

    public Object send(String wsdl, String method, Object... params) {

        // 创建动态客户端
        JaxWsDynamicClientFactory dcf = JaxWsDynamicClientFactory.newInstance();
        Client client = dcf.createClient(wsdl);
        // 需要密码的情况需要加上用户名和密码
        // client.getOutInterceptors().add(new ClientLoginInterceptor(USER_NAME, PASS_WORD));
        Object objects = new Object();
        try {
            // invoke("方法名",参数1,参数2,参数3....); 这里注意如果是复杂参数的话，要保证复杂参数可以序列化
            objects = client.invoke(method, params);
        } catch (Exception e) {
            log.error("ws调用异常: {}", e.getMessage());
        }
        log.info("返回数据: {}", objects);
        return objects;
    }
}
```

#### 测试用例示例
```
@RunWith(SpringRunner.class)
@SpringBootTest(properties = {"spring.profiles.active=dev"})
public class WsInvokeClientTest {

    @Autowired
    private WsInvokeClient wsInvokeClient;

    @Test
    public void testSend(){

        // 这里调用其他语言的ws接口时,会有类型转换问题 例: BigInteger,目前调用的是python3开发的ws接口
        Object send = wsInvokeClient.send("http://ip:port/?wsdl", "say_hello", "Yan", BigInteger.valueOf(22L));
        Assert.assertNotNull(send);
    }
}
```

[springboot cxf官网](https://cxf.apache.org/docs/springboot.html)
