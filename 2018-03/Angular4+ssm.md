# angular4+ssm
> 前端用angular4编写，而后台是spring+springmvc+mybatis，如何把angular项目放回到后台项目里呢？

# 前端项目打包
打包命令, [命令参数](https://github.com/angular/angular-cli/wiki/build)
```
ng build --target=production --environment=prod --base-href /
```


## 后端项目拉包
> 项目使用 gradle构建

1. *添加依赖* `dependencies `
```
dependencies {
  ...
	classpath ('org.ajoberstar:gradle-git:1.7.2')
}
```
2. *建立task*
```
def gitUrl = 'git@xxxxx/xxxx.git'
def branch = 'origin/master'
def webappPath = 'projectName/src/main/webapp/' //项目路径

task getFrontend(type: Copy) {
	println 'getFrontend start.'
	def frontendSrcPath = webappPath + 'frontend-src'
	def frontendDistPath = webappPath + 'frontend'
	delete frontendSrcPath
	delete frontendDistPath
	def grgit = Grgit.clone(dir: file(frontendSrcPath), uri: gitUrl)
	println 'gitClone start, url=' + gitUrl + ', branch = ' + branch
	grgit.checkout(branch: branch, createBranch: false)
	grgit.close()
	println 'gitClone end'
	ant.move(file: webappPath + 'frontend-src/frontend', tofile: frontendDistPath)
	delete frontendSrcPath
	grgit = Grgit.open()
	grgit.add(patterns: [webappPath])
	grgit.close()
	println 'getFrontend end.'
}
```

## 后端细节处理
### 路由处理
#### ，那怎么才能其他页面?

1. 把Anuglar打包后的包放到后端项目的resource路径
配置mvc:resources
`<mvc:resources mapping="/app/**" location="/frontend/"  order="1"/>`
2.  由于angular是单页应用，所以只有index.html，因此要把所有请求angular的都转发到index
```
Pattern path = Pattern.compile(request.getContextPath() + '/app/.*');
if(path.matcher(request.getRequestURI()).matches()) {
    RequestDispatcher rd = request.getRequestDispatcher("/app/index.html");
    rd.forward(request, response);
} else {
    filterChain.doFilter(request, response);
}
```
3. image url 问题
augular的[bug](https://github.com/angular/angular-cli/issues/9347)

[bug](https://github.com/angular/angular-cli/issues/9835)

```
A browser only applies the base href to relative assets. The baseHref option is generally the more appropriate option to use (vs. the deployUrl option). Changing the <img src="/assets/sample.png"> to <img src="assets/sample.png"> should allow the browser to apply the configured base href value.

Note also that based on the HTML spec., the base HREF value should end with a / to indicate the last path segment is a directory and not a file.
```
