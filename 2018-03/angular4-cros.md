# Angular4，开发环境下跨域,session共享问题
> 后台：spring + springMvc + mybatis

##场景
angular4项目，前端使用`ng serve`启动后,默认是localhost:4200.

ssm后台，tomcat启动后，localhost:8080

### 问题1:
localhost:4200请求调用localhost:8080
```

Failed to load resource: the server responded with a status of 403 (Forbidden)

No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'http://localhost:4200' is therefore not allowed access. The response had HTTP status code 403.
```

### 解决1：
applicationContext添加CorsConfiguration
```
<bean id="corsConfiguration" class="org.springframework.web.cors.CorsConfiguration">
     <property name="allowedMethods" value="*"/>
     <property name="allowedHeaders" value="*"/>
     <property name="allowCredentials" value="true"/>
     <property name="allowedOrigins">
         <list>
             <value>http://localhost:4200</value>
         </list>
     </property>
 </bean>
```
### 问题2:
localhost:8080有filter，`session == null`则跳去登录页面，但`localhost:4200`调用了`localhost:8080/login.do => return true`,还是被filter拦截

#### 原因分析
`login.do`登录虽然成功了,但`sessionId还存放在localhost:8080这个域下`,`localhost:4200`无法带着sessionId请求后台

#### 解决，请求加入withCredentials(非线上情况)
```
@Injectable()
export class CredentialsInterceptor implements HttpInterceptor {

  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {


    const newReq = req.clone({
      withCredentials: environment.withCredentials
    });
    return next.handle(newReq);
  }
}
```
