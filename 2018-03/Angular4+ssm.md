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
