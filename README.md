## onlyoffice documentserver 魔改重新编译步骤记录

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

# 下载中文字体包，浏览器打开 https://github.com/douguohai/build_tools/blob/main/fonts/mini_fonts.zip 下载字体包

rm -rf /usr/local/share/fonts/*

rm -rf /var/www/onlyoffice/documentserver/core-fonts/*

unzip mini_fonts.zip

docker cp mini_fonts 578333003b68:/usr/share/fonts

cd /usr/share/fonts/

/usr/bin/documentserver-generate-allfonts.sh

exit

docker restart 578333003b68
```

### 删除插件模块
```shell
docker exec -it 578333003b68 /bin/bash

cd /var/www/onlyoffice/documentserver/sdkjs-plugins/

rm -rf highlightcode/ macros/ mendeley/ ocr/ photoeditor/ speech/ thesaurus/  zotero/ youtube/ translator/

exit

docker restart 578333003b68
```

### 添加中文字号
```shell

docker cp  zxfile_onlyoffice:/var/www/onlyoffice/documentserver/web- apps/apps/documenteditor/main/app.js  app.js

#使用vim修改app.js 需要注意：编辑器修改会有问题

# 找到{value:8,displayValue:"8"} 在其前添加
{value:42,displayValue:"初号"},{value:36,displayValue:"小初"},{value:26,displayValue:"一号"},{value:24,displayValue:"小一"},{value:22,displayValue:"二号"},{value:18,displayValue:"小二"},{value:16,displayValue:"三号"},{value:15,displayValue:"小三"},{value:14,displayValue:"四号"},{value:12,displayValue:"小四"},{value:10.5,displayValue:"五号"},{value:9,displayValue:"小五"},{value:7.5,displayValue:"六号"},{value:6.5,displayValue:"小六"},{value:5.5,displayValue:"七号"},{value:5,displayValue:"八号"},

docker cp app.js zxfile_onlyoffice:/var/www/onlyoffice/documentserver/web-apps/apps/documenteditor/main/  有app.js.gz的话删掉

#本地清缓存，验证
```

### 中文的字体排序在前,在第一步添加中文字体时 用工具 fontcreate12的版本把英文名称修改时前面加*这样排序时字体就会根据字母排序到前面
```shell
1. 下载字体包和FontCreator,安装并打开 FontCreator  软件
https://www.high-logic.com/FontCreatorSetup-x64.exe
https://github.com/douguohai/build_tools/blob/main/fonts/mini_fonts.zip

2. 打开某个字体 File->open->选择字体文件
3. 打开字体后 Font->propertises->General->Family Name-> 修稿字体英文名称
4. 可在字体名称前方添加*,或者使用全键盘[带数字区域的键盘],在英文名称前面输入
5. 导出字体另存为ttf文件 File->Export Font -> Export Desktop font ttf
6. 将字体文件导入容器 重复 /usr/bin/documentserver-generate-allfonts.sh

```
