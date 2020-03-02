# 增加了 github和gitee 做图床的支持方式

创建一个公开的仓库做图床，对于个人用户，简单的小量图片上传需求想找个免费的，就比较方便



## 使用

修改配置
plugins/image/upload.js

```
setting.target 图片上传方式


setting.github 下
			token: 'xxx', // token ,建议只用有提交权限的（安全）
            userName: 'xxxx', //用户名
            repositorie: 'gitnote-images', //仓库名


setting.gitee 下
			token: 'xxx', // token  
            userName: 'xxx', //用户名
            repositorie: 'myNote-img', //仓库名

```



## 申请token

先登录

* github申请token
https://github.com/settings/tokens


* gitee申请token
https://gitee.com/profile/personal_access_tokens




# typora-plugins-win-img 原仓库README
### 介绍

解决Windows下，typora不支持粘贴自动上传图片到服务的问题。

### 背景

经常在Windows用typora的小伙一定遇到过一个问题：不管是用截图工具截图后直接粘贴，还是通过选择文件夹选择图片的方式，在typora下都会是图片的本地链接，并不会给你上传到远程服务器。

这样就会导致个尴尬的问题，你辛辛苦苦写的图文并茂内容发送给其他小伙伴时，对方却完全看不到那些图片。然后得找个地方把图片上传上去后，一张张替换成网络图片，实在麻烦。

> PS：其实还有种解决方式，就是设置typora插入图片时使用相对位置，并把它copy到指定的目录下，这样发送给别人的时候连带图片文件一起发送过去，对方也能愉快的浏览

作为码农，实在没法忍受这样的事情，所以尝试自己去写个typora“插件”（typora并不支持插件功能，实际做法是强行加代码）解决这个问题。

![演示截图](https://static.jiebianjia.com/typora/4077e33ef099391766d89fc50e669226.png)

### 使用

typora-plugins-win-img 插件在编辑时，跟之前没有任何差别。不论是直接粘贴QQ、微信等工具的截图，还是通过“编辑->图片工具->插入本地图片”，都会自动帮你将图片上传到网络服务器，并替换文件中的图片地址为网络图片地址。

注意的小细节：

1. 如图片原本就是网络图片地址，插件将保持原链接不处理（正则匹配：`/^(https?:)?\/\//i`）；
2. 如发现图片链接还是本地文件地址，没有被正常上传，可以点击下对应的图片将再次触发上传操作；
3. 不论图片上传成功或者失败，编辑器顶部都会有提醒；

### 安装

**安装教程环境说明：**

- typora版本：0.9.68 (Windows x86) （[去下载](https://typora.io/windows/typora-update-ia32-0320.exe)）
- typora安装目录：`C:\Program Files (x86)\Typora` ，可以安装在其他目录

**安装步骤：**

1. 下载插件代码；
2. 复制插件相关代码文件：`window.html`、`plugins`；
3. 将复制的插件代码文件，粘贴到typora安装目录下的 `resources\app` 文件夹下；
4. 安装完成，重启typora

**插件配置：**

插件默认会将图片上传到本人个人站点，不能保证一直给大家提供服务，所以按照好插件后，强烈建议你换成自己的图片上传服务器。

更换图片上次接口地址，打开 `plugins/image/upload.js` 文件，拉到最下面 将最后一行的 `$.image.init();` 按照下面的说明进行配置：

```javascript
//将图片上传地址换成你自己的后端接口，由于调用时不带登录态，请注意接口安全别被坏人利用
//为了方式坏人利用你服务器接口，插件支持设置请求头，可一定程度避免被利用
//接口协议：
//请求方式：POST
//请求参数：data:image/png;base64,xxxxxx （图片原转换成base64后的值）
//成功响应：{'code':0, 'message':'成功', 'data':{'url'=>'imageURL'}} 
//失败响应：{'code':x, 'message':'错误原因', 'data':null} 失败时，code必须未非0
//后端接口代码可以参考代码文件：`upload.php`
$.image.init({
    //默认上传地址 https://jiebianjia.com/typora-plugins/upload.html
    url:"https://you-server/the-image-upload-path",
    headers:{
        //默认: token:B40289FC92ED660F433BF0DB01577FDE
        key:"value"  //自己定义好，并在接口里面检查避免坏人利用你接口
    }
});
```


**注意事项：**

1. 本插件是基于typora：`0.9.68` 版本编写的，其他版本尚未测试过；
2. `window.html` 代码文件，为typora自带文件，复制过去会替换源安装文件，以防万一可以先对它进行备份；
3. Windows 系统盘默认会保护起来，可能需要系统管理才能操作这些文件，如粘贴失败注意看是否权限问题；
4. 默认本地图片，将会被上传到 [街边价](https://jiebianjia.com) 这个网站，本着方便使用的原则提供了默认图片地址，但本站点属于个人站点，如使用人太多会限制使用（包括但不限于不允许上传、清理已上传文件等）；【！！重要！！】
5. 由于`第4点`，强烈建议你按照 `插件配置` 设置你自己的后端图片上传地址；