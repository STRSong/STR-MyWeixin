# songminiprogram-STR少寒健康防疫小程序说明

**@author：==宋佳敏==**

**@date:2020-3-21**

## 一、小程序功能介绍

* 小程序包括三大模块：疫情防疫、健康登记、实时疫情
* 疫情防疫：用于宣传防疫相关知识，如疫情相关的新闻以及视频等
* 健康登记：用于用户健康状况打卡，采集用户身体健康数据，进行统计
* 实时疫情：此功能为外链显示百度的全国疫情实时动态的网页，帮助用户了解实时疫情（由于个人小程序无法配置业务域名，所以使用<web-view>功能，所以此功能只要在调试状态正常运行）



## 二、小程序目录结构说明

![](https://gitee.com/STRSong/NoteImage/raw/master/img/20200321222836.png)

## 三、小程序界面展示

### 1.欢迎页面

![](https://gitee.com/STRSong/NoteImage/raw/master/img/20200321224018.png)



### 2.疫情防范页面

![](https://gitee.com/STRSong/NoteImage/raw/master/img/20200321224132.png)

> **说明**：
>
> * 页面头部由一个轮播图片框组成，点击轮播框中的图片会跳转到相应的新闻文章的详情页面
> * 在轮播框下面由一个video视频播放框，点击可以播放视频内容，视频内容为新华网的疫情解“疫”视频
> * 在视频下方就是一些防疫文章的简介内容展示（如下图），内容包括了新闻作者的头像、名字、发布时间、简介图片、简介说明、点赞转发数目，点击内容中的图片可以跳转到问斩详情页面
> * 该页面使用了<official-account></official-account>组件，如果小程序绑定的公众号，在用户扫码情况下进入该页面会展示所关联公众号的信息，点击可以前往查看



![](https://gitee.com/STRSong/NoteImage/raw/master/img/20200321224415.png)

#### 2.1 新闻文章详情页面



![](https://gitee.com/STRSong/NoteImage/raw/master/img/20200321224504.png)

> **说明**：
>
>  * 新闻文章的详情页面头部由新闻的简介图片组成
>  * 在简介图片的下方包括了作者信息，发布时间文章标题信息，在右侧的两个按钮分别实现了文章内容的
>     * 收藏：将用户的收藏点击的行为进行记录并本地化，在下次点击同一篇文章时，收藏状态依然存在
>     * 分享：弹出分享框如下图1，分享到朋友圈、分享到QQ空间、分享到微博（此功能的转发并不能真的实现，因为该过程调用了外部APP的API,没有这个权限）
>     * 分享2：在文章的最后还有一个绿色的分享按钮，该功能时键文章进行微信好友的内部分享，已经实现，通过给button按钮中添加open-type="share"属性完成，效果如下图2



<img src="https://gitee.com/STRSong/NoteImage/raw/master/img/20200321230623.png" style="zoom: 80%;" /><img src="https://gitee.com/STRSong/NoteImage/raw/master/img/20200321230542.png" style="zoom: 80%;" />







### 3.健康登记页面

![](https://gitee.com/STRSong/NoteImage/raw/master/img/20200321224809.png)

> **说明**：
>
>  * 健康登记页面，页面主要由一个表单组成
>  * 其中表单中的==位置信息是根据用户的定位信息进行自动加载的==（该功能实现调用了腾讯地图的插件，具体实现见 **四、核心代码**）
>  * 表单的提交进行为空验证，表单中的内容有必填选项和选填选项两种，如果必填选项的内容没有完整的填写好，在提交时将会进行提醒，并且不予提交。如下图所示。
>  * 表单最后一项信息属实保证条例默认自动勾选，当提交表单时未勾选，将会进行提示，并且不予提交。
>
> 

![](https://gitee.com/STRSong/NoteImage/raw/master/img/20200321224922.png)



### 4.实时疫情页面

![](https://gitee.com/STRSong/NoteImage/raw/master/img/20200321224948.png)

> **说明**：
>
>  * 此功能是使用<web-view>组件进行跳转实现，<web-view>组件可以将外链地址的页面自动铺满整个屏幕
>  * 但是该功能在个人小程序中只能调试运行，因为微信小程序当前个人公众号是不允许配置业务域名的。如果是企业小程序，则可以通过配置业务域名来正常使用改功能。



## 四、核心代码

### 1.轮播框跳转

```js
//home.js  
//点击轮播图片跳转详情页
  tonewsDetail(event){
    console.log(event)
    let index= event.target.dataset.index;
    wx.navigateTo({
      url: '/pages/newsDetail/newsDetail?index=' + index,
    })
  }
```

> 在home.wxml中给swiper组件绑定catchtap="tonewsDetail"事件
>
> 由于需要点击对应图片跳转到对相应文章，在image标签上绑定data-index='0'提交数据的页数，触发事件时取出
>
> 过程涉及target和currentTarget的区别

### 2.视频播放封面设置

> video组件默认视频封面是黑色的

```html
<!--home.wxml-->
  <video show-center-play-btn='false' objectFit='cover' id="myVideo" class="videoStyle pr"  
src="https://vodpub1.v.news.cn/original/20200307/b3d9733f81ac44dfae0dad2c9661b1a2.mp4" >
    <view style="display: {{tab_image}};">
      <cover-view class="controls pa" id="controls" > <!--  视频 封面图片   -->
        <cover-image src="http://www.xinhuanet.com/video/2020-02/26/1210491269_15827143204031n.jpg" style='height:455rpx;'/>
      </cover-view>
      <!-- 中间的播放图片标志 -->
      <cover-view class="play pa" style="z-index:200;" bindtap="videoPlay">
        <cover-image bindtap="bindplay" data-id="1" src="/images/tab/play.png" style="width:100rpx;"/>
      </cover-view>
      </view>
  </video><!-- 视频--> 
```

```js
<!--事件触发代码-->
  // 点击cover播放
  videoPlay: function (e) {
    console.log('点击播放');
    console.log(e);
    //隐藏封面图和播放图标
    this.setData({
      tab_image: "none"    //tab_image 来控制封面图 
    }),
      this.videoCtx.play();
  }
```



### 3.文章简介内容展示

```js
let datas = require('../../datas/news-data.js');//获取数据内容
  // 页面的初始数据
  data: {
    listArr: []
  },
      
    onLoad: function (options) {
    //设置数据
    this.setData({
      listArr: datas.news_datas,
      tab_image:null
    })
  },
```

```html
<!--home.js 对数据进行遍历，调用模版页面-->
  <block wx:for='{{listArr}}' wx:key='{{index}}'>
    <view catchtap="toDetail" data-index="{{index}}">
      <template is="homeTem" data="{{...item}}"/>
    </view>
  </block>
```

```html
<!--模版页面-->
<template name="homeTem">
  <view class="container1">
    <view class="header">
      <image src="{{authorImg}}"></image>
      <text style="padding-right:10rpx;">{{author}}</text>
      <text>{{date}}</text>
    </view>
    <view class="author"><text>{{title}}</text></view>
    <image class="centerImages" src="{{titleImg}}"></image>
    <view class="content">
      <text class="context">{{introduce}}</text>
    </view>
    <view class="footer">
      <image src="http://picnew13.photophoto.cn/20190505/dianzantubiao-33017761_1.jpg"></image>
      <text>{{seeNum}}</text>
      <image src="http://hbimg.b0.upaiyun.com/7c7157832af74a704121a912f006614724910d241c50-YrRmjG_fw658"></image>
      <text>{{colNum}}</text>
    </view>
  </view>
</template>
```

### 4.文章详情页面收藏功能实现

```html
<!--绑定handleColleted事件，通过data数据中的isCollected判断生成对应的图片-->
<image catchtap="handleColleted" src="/images/icon/collection-anti.png" wx:if="{{!isCollected}}"></image>
<image catchtap="handleColleted" src="/images/icon/collection.png" wx:if="{{isCollected}}"></image> 
```

```js
  //处理用户收藏
  handleColleted() {
    let isCollected = !this.data.isCollected;
    //更新状态
    this.setData({
      isCollected: isCollected
    });

    //提示用户
    let title = isCollected ? '收藏成功' : '取消收藏';
    wx.showToast({
      title,
      icon: 'success',
      duration: 2000
    });

    //缓存数据到本地
    let index = this.data.index;
    wx.getStorage({
      key: 'isCollected',
      success: function (res) {
        let obj = res.data;
        obj[index] = isCollected;
        wx.setStorage({
          key: "isCollected",
          data: obj,
          // success:function(){
          // }
          success: () => {
            console.log('success')
          }
        })
      },
      //第一次，未获取进行创建
      fail: () => {
        let obj = {};
        obj[index] = isCollected;
        wx.setStorage({
          key: "isCollected",
          data: obj,
          success: () => {
            console.log('success')
          }
        })
      }
    });
  },
```

### 5.文章详情页面文章内容加载

> 文章内容的数据格式如下图

![](https://gitee.com/STRSong/NoteImage/raw/master/img/20200322085654.png)

**在进行内容遍历生成时，通过wx:if来判断内容的给是来生成不同类型的内容**

```js
  <block wx:for='{{detailObj.content}}' wx:key='{{index}}' >
    <text class='content-text' wx:if='{{item.type=="text"}}'>{{item.data}}</text>
    <image class='content-image' wx:if='{{item.type=="image"&&detailObj.newsId!=0}}' src='{{item.data}}'></image>
    <image class='content-image2' wx:if='{{item.type=="image"&&detailObj.newsId==0}}' src='{{item.data}}'></image>
  </block>
```

### 6.用户位置的自动确定(需要用户授权)

```json
//用户授权
"sitemapLocation": "sitemap.json",
  "permission": {
    "scope.userLocation": {
      "desc": "你的位置信息将用于小程序位置接口的效果展示"
    }
```

```js
// 引入SDK核心类
var QQMapWX = require('../../lib/qqmap-wx-jssdk1.2/qqmap-wx-jssdk.js');
import WxValidate from "../../lib/WxValidate.js";
var qqmapsdk;
```

```js
  /* 页面的初始数据*/
  data: {
    listArr:[],
    flag : false,
    localtion:null
  },
```

```js
  onReady: function () {
    // this.initValidate();
    let loc=this;
    // 实例化API核心类
    qqmapsdk = new QQMapWX({
      key: 'VIGBZ-SL5L4-PXNU2-DNLRH-UOVK7-JSFLV'
    });

    //获取用户位置信息
    wx.getLocation({
      type: 'wgs84',
      success(res) {
        const latitude = res.latitude;
        const longitude = res.longitude;
        //打印经纬度
        //console.log(latitude,longitude);
        //提供由坐标到坐标所在位置的文字描述的转换。输入坐标返回地理位置信息和附近poi列表
        qqmapsdk.reverseGeocoder({
          location: {
            latitude,
            longitude
          },
          success: function (res) {//成功后的回调
            console.log(res);
            let local = res.result.address;
            loc.setData({
              flag : true,
              localtion: local
            });
            //打印位置信息
            //console.log(local);
            var res = res.result;
            var mks = [];
            //当get_poi为0时或者为不填默认值时，检索目标位置，按需使用
            mks.push({ // 获取返回结果，放到mks数组中
              title: res.address,
              id: 0,
              latitude: res.location.lat,
              longitude: res.location.lng,
              iconPath: './resources/placeholder.png',//图标路径
              width: 20,
              height: 20,
              callout: { //在markers上展示地址名称，根据需求是否需要
                content: res.address,
                color: '#000',
                display: 'ALWAYS'
              }
            });
          },
          fail: function (error) {
            console.error(error);
            loc.setData({
              localtion: '获取失败'
            });
          },
          // complete: function (res) {
          //   console.log(res);
          // }

        })
      }
    });
  },
```

==**特别注意**==：

![](https://gitee.com/STRSong/NoteImage/raw/master/img/20200322090434.png)

**此处的key值为本人自己的腾讯地图账号生成的key值，如果要使用这个功能，请将Key换成你自己生成的key**

**如需使用地图的其他功能可以参考官方文档：**

1. https://lbs.qq.com/qqmap_wx_jssdk/index.html
2. https://developers.weixin.qq.com/miniprogram/dev/api/location/wx.getLocation.html



### 7.表单内容验证

```js
  formSubmit: function (e) {
    //console.log('form发生了submit事件，携带数据为：', e.detail.value);
    const params = e.detail.value;
    // 传入表单数据，调用验证方法
    if (params.temp == '' || params.问题1 == '' || params.问题2 == '' || params.问题3 == '' || params.问题4 == ''
      || params.问题5 == '' || params.问题6 == '' || params.问题7 == '' || params.问题8 == '') {
      wx.showModal({
        title: '提示',
        content: '请将需要填写的信息填写完整',
        success: function (res) {
          if (res.confirm) {
            console.log('用户点击确定')
          } else {
            console.log('用户点击取消')
          }

        }
      });
      return false
    }
    if (params.checkbox == '') {
      wx.showModal({
        title: '提示',
        content: '请勾选保证条例，保证数据真实',
        success: function (res) {
          if (res.confirm) {
            console.log('用户点击确定')
          } else {
            console.log('用户点击取消')
          }

        }
      });
      return false
    }
    console.log('将要提交的表单信息：', params);
    wx.showToast({
      title: '提交成功!',
      icon: 'success',
      duration: 2000
    })
  },
```



### 8.外链跳转

```html
<text>正在前往...</text>
<web-view src='https://voice.baidu.com/act/newpneumonia/newpneumonia'></web-view>
```

>  该功能在个人小程序中只能调试运行，因为微信小程序当前个人公众号是不允许配置业务域名的。如果是企业小程序，则可以通过配置业务域名来正常使用改功能。



