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

```html
/**
 * @description POST请求
 * @author Songjp
 * @augments fullUrl 请求地址
 * @augments param 请求参数
 * @augments token 默认带token
 */
function doPost(fullUrl, param, token = true) {
  return new Promise(function (resolve, reject) {
    let _params = {}
    if (token) {
      _params = {
        openId: wxsystem.getToken() || '',
        clientType: wxsystem.getClientType() || '',
        clientId: utils.getUUIDFromSession()
      }
    }
    _params = Object.assign(_params, param)
    console.log(`请求地址：${fullUrl}，请求参数：`)
    console.log(_params)
    wx.request({
      url: fullUrl,
      data: _params,
      method: 'POST',
      success: function (res) {
        console.log(res.data)
        if (res.statusCode === 200) {
          if (res.data.code === '0') {
            resolve(res.data)
          } else if ((res.data.code !== '0') && res.data.message) {
            wepy.showToast({
              title: res.data.message,
              icon: 'none',
              duration: 1500
            })
          } else {
            wepy.showToast({
              title: '发生未知错误，请联系客服!',
              icon: 'none',
              duration: 1500
            })
          }
        } else if (res.statusCode === 401) {
          wepy.showToast({
            title: '未授权 ，登录失败！',
            icon: 'none',
            duration: 1500
          })
        } else if (res.statusCode === 404) {
          wepy.showToast({
            title: '服务器找不到您所请求的文件或脚本！',
            icon: 'none',
            duration: 1500
          })
        } else if (res.statusCode === 500) {
          wepy.showToast({
            title: '服务器发生未知错误！',
            icon: 'none',
            duration: 1500
          })
        } else {
          wepy.showToast({
            title: '生未知错误！',
            icon: 'none',
            duration: 1500
          })
        }
      },
      fail: function () {
        wepy.showToast({
          title: '网络超时，请重新操作！',
          icon: 'none',
          duration: 1500
        })
      }
    })
  })
}

```

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
```html
function updateManager() {
  const updateManager = wx.getUpdateManager()
  updateManager.onCheckForUpdate(function (res) {
    // 请求完新版本信息的回调
    if (res.hasUpdate) {
      wepy.showLoading({
        title: '更新中...'
      })
      updateManager.onUpdateReady(function () {
        wepy.hideLoading()
        wx.showModal({
          title: '更新提示',
          content: '新版本已经准备好，是否重启应用？',
          success(res) {
            wepy.hideLoading()
            if (res.confirm) {
              // 新的版本已经下载好，调用 applyUpdate 应用新版本并重启
              updateManager.applyUpdate()
            }
          }
        })
      })
    } else {
      wepy.hideLoading()
    }
  })
  updateManager.onUpdateFailed(function () {
    wepy.hideLoading()
    wx.showModal({
      title: '更新提示',
      content: '更新失败，请尝试重新打开程序！'
    })
  })
}
```

