---
title: 微信开发相关
date: 2020/4/10 16:34:30
categories:
- 博客
tags:
- 前端
---
##### **1.电子名片 项目结构**

###### 1.1目录截图

![](https://mind-node.github.io/image/image-20200318115552768.png)

###### 1.2 操作流程

​		使用hbuilderX工具打开uni-app项目，点击运行到小程序.			

###### 1.3 目录功能描述

```dif
┌─pages               				//页面文件夹
│  ├─index								
│  │  	 └─index.vue    			//展厅介绍
│  └─activity
│  │     └─activity.vue    			//当月活动
│  └─details   
│  │   	 └─details.vue    			//金融详情
│  └─financial    
│  │     └─financial.vue    		//当月金融
│  └─mainCar  
│  │     └─mainCar.vue    			//主推车型
│  └─maintenance    
│  │     └─maintenance.vue			//预约保养
│  └─person    
│  │     └─person.vue				//名片介绍
│  └─rescue    
│  │     └─rescue.vue				//一键救援
│  └─shop    
│  │     └─shop.vue					//会员商城
│  └─testDrive    
│        └─testDrive.vue			//预约试驾
│       
├─static           					//静态文件夹
├─main.js       					//入口文件
├─App.vue          					//项目根组件
├─manifest.json  					//多平台配置文件
└─pages.json 						//页面路由，导航，tabBar配置文件
```

###### 1.4 目录相关代码：

​	无

###### 1.5 目录说明或者备注：

​	**pages.json的配置项**

| 属性                                                         | 类型         | 必填 | 描述                    | 平台兼容   |
| :----------------------------------------------------------- | :----------- | :--- | :---------------------- | :--------- |
| [globalStyle](https://uniapp.dcloud.io/collocation/pages?id=globalstyle) | Object       | 否   | 设置默认页面的窗口表现  |            |
| [pages](https://uniapp.dcloud.io/collocation/pages?id=pages) | Object Array | 是   | 设置页面路径及窗口表现  |            |
| [easycom](https://uniapp.dcloud.io/collocation/pages?id=easycom) | Object       | 否   | 组件自动引入规则        | 2.5.5+     |
| [tabBar](https://uniapp.dcloud.io/collocation/pages?id=tabbar) | Object       | 否   | 设置底部 tab 的表现     |            |
| [condition](https://uniapp.dcloud.io/collocation/pages?id=condition) | Object       | 否   | 启动模式配置            |            |
| [subPackages](https://uniapp.dcloud.io/collocation/pages?id=subpackages) | Object Array | 否   | 分包加载配置            |            |
| [preloadRule](https://uniapp.dcloud.io/collocation/pages?id=preloadrule) | Object       | 否   | 分包预下载规则          | 微信小程序 |
| [workers](https://developers.weixin.qq.com/miniprogram/dev/framework/workers.html) | String       | 否   | `Worker` 代码放置的目录 | 微信小程序 |

##### **2.获取用户信息**	

###### 2.1 微信小程序登录

​	2.1.1 登录流程

​			![](https://mind-node.github.io/image/api-login.2fcc9f35.jpg)

​	2.1.2 登录功能描述	

​		1.调用 [wx.login()](https://developers.weixin.qq.com/miniprogram/dev/api/open-api/login/wx.login.html) 获取 **临时登录凭证code** ，并回传到开发者服务器。

​		2.调用 [auth.code2Session](https://developers.weixin.qq.com/miniprogram/dev/api-backend/open-api/login/auth.code2Session.html) 接口，换取 **用户唯一标识 OpenID** 和 **会话密钥 session_key**。

​		3.之后开发者服务器可以根据用户标识来生成自定义登录态，用于后续业务逻辑中前后端交互时识别用户身	份。

​	 2.1.3 登录相关代码：	

```javascript
		uni.login({
			     provider: 'weixin',
			     //scopes: 'auth_user',//授权类型，默认 auth_base。支持 auth_base（静默授权）/ 					auth_user（主动授权） / auth_zhima（芝麻信用）
			     success: (loginRes) => {
					console.log(loginRes);
					getApp().globalData.code = loginRes.code
					if(loginRes.code) {
						let data = {
							code : loginRes.code
						}
						that.$request.get(WECHART_LOGIN,data).then(res => {
							if(res.code === 0){
								getApp().globalData.openid = res.data.openid;
								getApp().globalData.session_key = res.data.session_key;
							}
						})
					}
				}
			})
```

​	2.1.4功能说明或者备注：

​			应用服务器地址需要配置

​			应用服务器接口返回数据根据不同数据格式需要解析

###### 2.2 获取用户当前位置

​	2.2.1 获取流程

​			1.弹窗让用户提供位置信息的授权

​			2.授权后通过接口返回坐标

​	2.2.2 获取位置相关代码

```javascript
			uni.getLocation({
					success(res){
						that.latitude = res.latitude;
						that.longitude = res.longitude
						that.circles[0].latitude = res.latitude;
						that.circles[0].longitude = res.longitude;
						that.markers[0].latitude = res.latitude;
						that.markers[0].longitude = res.longitude;
					},
					fail() {
						uni.getSetting({
							success(res) {
								 if (!res.authSetting['scope.userLocation']){
									 
								 }
								 else {
									 uni.showToast({
									 	icon:"none",
										title:"请在系统设置中打开定位服务"
									 })
								 }
							}
						})
					}
				})
```

​	2.1.3 功能说明或者备注：

​			1.工具中定位模拟使用IP定位，可能会有一定误差

​			2.使用第三方服务进行逆地址解析时，请确认第三方服务默认的坐标系，正确进行坐标转换

###### 2.3 获取用户手机号

​		2.3.1 获取流程

​			1.需要用户自己点击按钮，弹窗让用户提供手机信息的授权

​			2.授权后通过接口返回加密后的手机信息

​			3.将信息给到后端，解密后返回

​		2.3.2 获取用户手机号相关代码

```javascript
		   getPhoneNumber(e){
			   let that = this;
			   let data = {
				   openid : getApp().globalData.openid
			   }
			   that.$request.get(ALLOW_BIND,data).then(res => {
					if(res.data !== "已绑定") {
						if(e.detail.encryptedData){
							let data = {
								openid : getApp().globalData.openid,
								session_key : getApp().globalData.session_key,
								encryptedData : e.detail.encryptedData,
								iv : e.detail.iv
							}
							that.$request.post(BIND_SAVE,data).then(res => {
								console.log(res)
							})
						}else{
							uni.showToast({
								title: '你取消了授权',
								duration: 2000
							});
						}
					}
			   })
		   }
```

​		2.3.3 功能说明或者备注：

​			需要用户主动触发才能发起获取手机号接口，所以该功能不由 API 来调用，需用 [button](https://developers.weixin.qq.com/miniprogram/dev/component/button.html) 组件的点击来触发

###### 2.4 获取用户个人信息

​		2.4.1 获取流程

​			1.需要用户自己点击按钮，弹窗让用户提供个人信息的授权

​			2.授权后通过接口返回个人信息

​		2.4.2 获取用户个人信息相关代码

```javascript
			uni.getUserInfo({
					success(res) {
						let data = {
							openid : getApp().globalData.openid,
							sex : res.userInfo.gender,
							province : res.userInfo.province,
							imgUrl : res.userInfo.avatarUrl
						}
						that.$request.post(ADD_USER,data).then(res => {
							console.log(res)
						})
					}
				})
```

###### 2.5 获取设备信息

​	2.5.1 获取流程

​			1.调用接口getSystemInfo获取设备信息

​			2.调用接口getNetworkType获取网络类型

​	2.5.2 获取用户手机号相关代码

```javascript
		let res = uni.getSystemInfoSync()
			setTimeout(function() {
				getGlobalData.device = res.system.split(" ")[0];
				getGlobalData.device_height = res.screenHeight;
				getGlobalData.device_width = res.screenWidth;
				getGlobalData.os_name = res.system.split(" ")[0];
				getGlobalData.os_version = res.system.split(" ")[1];
				getGlobalData.language = res.language;
				getGlobalData.device_model = res.model;
				getGlobalData.device_brand = res.brand;
				getGlobalData.page_path = data.path;
			}, 10)
			uni.getNetworkType({
				success: function(res) {
					getGlobalData.network_type = res.networkType;
					console.log(getGlobalData)
				}
			})
```

​	2.5.3 功能说明或者备注：

​		[接口参考](https://developers.weixin.qq.com/miniprogram/dev/api/base/system/system-info/wx.getSystemInfo.html)

##### 3. 拼图功能

###### 	3.1 功能流程

1. 用户点击拼图按钮，显示拼图图标，以及图片上方选择框

2. 用户选择完需要拼图的图片后，点击开始拼图按钮

3. 输出图片


###### 	3.2 拼图功能主要代码

```javascript
			async toPuzzle() {
				let that = this;
				let res = [];
				this.selectPics.forEach(function(item,index){
					let arr = {};
					arr.url = item;
					arr.dx = 0;
					arr.dy = 167 * index;
					arr.dWidth = 250;
					arr.dHeight = 167;
					arr.type = "image"
					res.push(arr);
				 })
				try {
					console.log('准备生成:' + new Date())
					const d = await getSharePoster({
						_this: this, //若在组件中使用 必传
						type: 'testShareType',
						formData: {
							//访问接口获取背景图携带自定义数据
						},
						posterCanvasId: "canvas",	//canvasId
						delayTimeScale: 20, //延时系数
						background: {
							width: 250/0.75,
							height: 167*that.selectPics.length/0.75,
							backgroundColor: '#ffffff'
						},//图片背景
						drawArray : res,//控制所要画图的样式
						setCanvasWH: ({
							bgObj,
							type,
							bgScale
						}) => { // 为动态设置画布宽高的方法，
							this.poster = bgObj;
						}
					});
					console.log('海报生成成功, 时间:' + new Date() + '， 临时路径: ' + d.poster.tempFilePath)
					this.poster.finalPath = d.poster.tempFilePath;
					this.isShowPoster = true;
				} catch (e) {
					_app.hideLoading();
					_app.showToast(JSON.stringify(e));
					console.log(JSON.stringify(e));
				}
			}
```

###### 		3.3 功能说明或者备注

1. 注意自己的获取图片信息的api--getImageInfo 是否正常获取图片信息，否则绘制不出来

2. 可以自定义生成需要的样式


##### 4 滑块验证码功能

###### 	4.1 功能流程

1. 创建2个canvas，一个当作背景，一个当作拼图

2. 传入3个参数，裁剪区域的横坐标，裁剪区域的纵坐标，拼图的边长

3. 背景图使用fill()的方法裁剪出一个矩形

4. 拼图使用canvasToTempFilePath的方法裁剪成一个拼图图片

5. 小程序中的触控事件touchmove,touchend分别记录移动的距离和松手时的距离

6. 判断移动距离和x的差距，如果两者小于一定值，则验证成功，反之失败。


###### 	4.2 拼图功能主要代码

```javascript

		onReady:async function () {
			let that = this;
			let localImg = await this.downloadImg();
			console.log(localImg)
            //通过getSystemIfo获取，设备宽高，然后计算所需数值
			uni.getSystemInfo({
				success: function (res) {
					that.width = res.windowWidth;
					that.height = res.windowHeight;
				}
			})
            //创建两个Canvas,一个用来当验证背景（底），一个用来当拼接块
			const canvas = uni.createCanvasContext('canvas');
			const block = uni.createCanvasContext('block'),
			three = uni.createCameraContext('three');
			const img = localImg.path,
			canvas_width = that.width ,
			canvas_height = that.height * 0.3;
			console.log(img)
			let l = 50, 
			x =	150+Math.random()*(canvas_width-l-150), 
			y = 10+Math.random()*(canvas_height-l-10);
			that.block_w = l;
			that.y = y;
			that.x = x;
			canvas.drawImage(img, 0, 0, canvas_width, canvas_height);
			canvas.draw(false, setTimeout(() => {
				uni.canvasToTempFilePath({
					x: x,
					y: y,
					width:l,
					height: l,
					canvasId: 'canvas',
					fileType: 'png',
					success(res) {
						console.log(res.tempFilePath)
						that.pic = res.tempFilePath;
					},
					fail: err => {
						console.log(err)
					}
				}, this)
			}, 800))
			block.beginPath()
			block.moveTo(x,y)
			block.lineTo(x,y+l)
			block.lineTo(x+l,y+l)
			block.lineTo(x+l,y)
			block.globalCompositeOperation = 'xor' 
			block.fill()
			block.drawImage(img, 0, 0, canvas_width, canvas_height);
			block.draw() 
		}
```

###### 	4.3 功能说明或者备注		

1. 绘制所需要的图片，需要先下载到本地缓存


##### 5. 小程序跳转

###### 5.1 功能流程

1. 用户点击，跳转其他小程序

###### 5.2 拼图功能主要代码

```javascript
				uni.navigateToMiniProgram({
					appid:"wx33cf03b165de3b0f",
					extraData:{
						data:"111"
					},
					success(){
						console.log("成功")
					},
					complete(){
						console.log("调用了")
					}
				})
```

###### 5.3 功能说明或者备注	

1. 将所要携带的参数放到extraData里

 	2. 在小程序的onShow onLaunch可以获取到传递过去的参数