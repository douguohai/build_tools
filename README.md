## 魔改重新编译，突破20长链接限制

### 下载 onlyoffice documentserver 魔改过的构建工具 build_tools
```bash
git clone https://github.com/douguohai/build_tools.git
```


### 下载 onlyoffice documentserver 官方源码，使用 git 切换版本至 v7.1.1，同时下载 documentserver 中链接的子项目，下载完成后将 documentserver 的组件移动到上层目录：
```bash
git clone --recursive -b v7.1.1 https://github.com/ONLYOFFICE/DocumentServer.git &&\ 
mv ./DocumentServer/core ./DocumentServer/core-fonts ./DocumentServer/dictionaries ./DocumentServer/sdkjs ./DocumentServer/sdkjs-plugins ./DocumentServer/server ./DocumentServer/web-apps . &&\
rm -rf DocumentServer/
```

### 下载 onlyoffice document-templates 官方源码，使用 git 切换版本至 v7.1.1.76：
```bash
git clone -b v7.1.1.76 https://github.com/ONLYOFFICE/document-templates.git

```

### 下载 onlyoffice document-server-integration 官方源码，使用 git 切换版本至 v7.1.1.76，同时下载 documentserver 中链接的子项目:
```bash
git clone --recursive -b v7.1.1.76 https://github.com/ONLYOFFICE/document-server-integration.git

```

### 下载 onlyoffice sdkjs-forms 官方源码，使用 git 切换版本至 v7.1.1.76：
```bash
git clone -b v7.1.1.76 https://github.com/ONLYOFFICE/sdkjs-forms.git

```

### 下载 onlyoffice ducumentbuilder 官方源码，使用 git 切换版本至 v7.1.1.76，同时下载 documentbuilder 中链接的子项目

```bash
git clone --recursive -b v7.1.1.76 https://github.com/ONLYOFFICE/DocumentBuilder.git
```

### 开始编译
```bash
./automate server --update=0
```


### make deb,clone 下面项目在build-tools 相同级别目录,执行下面命令，生成的 deb 包在 ./document-server-package/deb/ 路径下：
```shell
git clone -b v7.1.1.76 https://github.com/ONLYOFFICE/document-server-package.git
cd document-server-package
make deb
```
https://loongson-cloud-community.github.io/Loongson-Cloud-Community/%E7%A7%BB%E6%A4%8D%E6%89%8B%E5%86%8C/onlyoffice/#_12

### Docker 镜像制作,clone 魔改后的构建仓库
```shell

git clone  https://github.com/douguohai/Docker-DocumentServer.git

cd ./Docker-DocumentServer && mkdir deb
cp ../document-server-package/deb/onlyoffice-documentserver_0.0.0-0_amd64.deb deb/onlyoffice-documentserver_amd64.deb
docker build -t douguohai/onlyoffice-documentserver:7.1.1.76 .

```

### 添加中文字体
```shell
docker pull douguohai/onlyoffice-documentserver:7.1.1.76

docker run -i -t -d --restart=always --name onlyoffice-documentServer-server -p 30080:80 \
    -v /app/onlyoffice/DocumentServer/logs:/var/log/onlyoffice  \
    -v /app/onlyoffice/DocumentServer/data:/var/www/onlyoffice/Data  douguohai/onlyoffice-documentserver:7.1.1.76
    
docker exec -it 578333003b68 /bin/bash


# 下载中文字体包
wgte 

cd /usr/share/fonts/

docker cp mini_fonts/* e46202601a33:/usr/share/fonts

/usr/bin/documentserver-generate-allfonts.sh
```

### 删除插件模块
```shell
docker exec -it 578333003b68 /bin/bash

cd /var/www/onlyoffice/documentserver/sdkjs-plugins/

rm -rf highlightcode/ macros/ mendeley/ ocr/ photoeditor/ speech/ thesaurus/  zotero/ youtube/

docker restart 578333003b68
```