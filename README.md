# wepyMiniprogram
基于wepy的个人常用开发框架

### 安装使用

下载完成之后
```console
npm install
```
项目中提供了开发环境和生产环境，分别在config文件中，可根据自己的服务修改请求地址

安装成功之后  两种编译方式
```console
npm run dev
npm run build
```

### 主要文件夹如下：
* config 请求路径配置
* components 组件文件夹
* images  图片途径
* lib  常用工具
* pages  视图view
* server 请求控制层 存放请求api方便维护


### request.js
* doPost(fullUrl,param,token)方法
* 主要处理了异步请求使用promise返回
* 会根据当前缓存处理请求是否带Token
* 返回时会根据业务只做正确的返回接收，其余全部抛出提示。

### socket.js
onLoadSocket(url, param)方法
具体使用如下：  
```html
    // socket.onLoadSocket(config.socketUrl, {}).then(res => {
    //   wx.sendSocketMessage({
    //     data: '发送一条消息1111'
    //   })
    //   wx.onSocketMessage(function(res) {
    //     console.log('收到服务器内容：' + res.data)
    //   })
    // })
```
### wx-system.js
这里主要有一些wx的api做了一些处理
比如用户登录+用户基础信息获取

### wx-update.js
微信版本更新方法，可直接调用即可
