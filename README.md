# 自定义jenkins镜像
## 1, 官方lts版本
```
https://hub.docker.com/r/jenkins/jenkins/tags?page=1&name=2.332.3
```
参考说明文档
```
https://github.com/jenkinsci/docker/blob/master/README.md
```
## 2, 制作需要的插件
```shell
# 本地运行官方jenkins
docker run -u root  -d -p 8080:8080 -p 50000:50000 --restart always -v /var/jenkins_home:/var/jenkins_home -v /var/run/docker.sock:/var/run/docker.sock --name jenkins  jenkins/jenkins:2.332.3-lts
# 查看登录密码
docker logs -f jenkins
# 登录后安装个人需要的所有插件
如 pipeline
   blueocean
   git 
   kubernetes 
   ...  等等
# 安装完成后导出插件列表到plugins.txt
JENKINS_HOST=admin:123456@192.168.174.152:8081
curl -sSL "http://$JENKINS_HOST/pluginManager/api/xml?depth=1&xpath=/*/*/shortName|/*/*/version&wrapper=plugins" | perl -pe 's/.*?<shortName>([\w-]+).*?<version>([^<]+)()(<\/\w+>)+/\1 \2\n/g'|sed 's/ /:/'>plugins.txt
   
```
## 3， 编写Dockerfile,构建镜像
vi Dockerfile
```shell
FROM jenkins/jenkins:2.332.3-lts
COPY --chown=jenkins:jenkins plugins.txt /usr/share/jenkins/ref/plugins.txt
RUN jenkins-plugin-cli -f /usr/share/jenkins/ref/plugins.txt
```
构建镜像
```shell
# ls
Dockerfile  plugins.txt
# docker build -t jiamiao442/jenkins:2.332.3-lts .
```