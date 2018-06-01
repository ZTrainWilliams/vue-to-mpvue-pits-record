###vue项目使用mpvue转小程序填坑日志

# 重点避免点
	1. template不能使用methods方法
	解决方案： 使用computed计算属性取代template内的方法计算进行显示
	2. mpvue不支持动态组件，使用v-for循环子组件，切换循环的数据源这时就构成的动态组件
	造成的问题：tab切换数据时，会造成视图的更新为上一次的数据源，实际data中的数据已经切换，或者视图位置已经占据，但是没有显示
	解决方案：切换数据时，先将数据清空，setTimeout再赋值新数据，这个时候视图就能响应
	3.小程序不支持cdn文件？

### rem转rpx尺寸错误
	小程序无法操作dom，所以无法计算根字体，rem转rpx容易出现误差，此时小于实际尺寸三分一。
	解决方案：使用VSC px to rem 插件单个文件全选 Alt + Z rem全部转成px，这个时候postcss-mpvue-wxss 转换成的rpx是正常尺寸。

### iconFont、图片等静态文件，无法打包进入dist文件夹中
	解决方案：把对应静态资源包放入dist文件夹中即可，在开发过程中，新编译文件不会覆盖静态资源包

### main.js对应app.json
	tabBar -> list -> pagePath: 'pages/index/main',pagePath需要在 pages中名字路径对应

### template内的v-for 出错
	v-for="(item, index) in arr" :key="index" ,  必须加上index和唯一的key
	且嵌套v-for不能使用同名索引(index)

### mixins 引用的组件无法声明
	避免在mixins文件中引用组件声明components，需在vue文件中引用声明

### scroll-view 滚动出现警告
	警告：Do not have scroll handler in current page: pages/index/main. Please make sure that scroll handler has been defined in pages/index/main, or pages/index/main has been added into app.json
	原因：滚动方法未用 @ 在template内声明
```vue
	<scroll-view :scroll-top="scrollTop" scroll-y="true" :style="'height:'+scrollHeight+'px;'" class="list" bindscrolltolower="bindDownLoad" bindscrolltoupper="topLoad" bindscroll="scroll"></scroll-view>
	<scroll-view :scroll-top="scrollTop" scroll-y="true" :style="'height:'+scrollHeight+'px;'" class="list" @scrolltolower="DownLoad" @scrolltoupper="topLoad" @scroll="scroll"></scroll-view>
```vue
	scrollTop、scrollHeight都必须设置，scrollHeight不能使用%，可以通过以下方式获取设置适应高度
```js
	var query = wx.createSelectorQuery();
	query.select('.item_tab').boundingClientRect();
	query.exec(function (r) {
		var h = r[0].height;
		wx.getSystemInfo({
			success:function(res){
				that.scrollHeight = res.windowHeight - h;
			}
		});
	})
```js

### scroll-view 绑定scroll事件，反复滚动容易造成抖动问题

### scroll-view 使用滚动加载在加载更多后会返回顶部
	有需要考虑使用 onReachBottom进行滚动的监听，在main.js设置window:{onReachBottomDistance:50},50为滚动到底部前的距离触发onReachBottom事件

### v-for组件无法同时使用v-model传值
	mpvue 无法编译

### this.$children调用组件函数，函数上下文不再是当前组件vue的this

### 计算属性无法使用 get set 重新赋值

### 使用vue路由时，生命周期created()函数中无法使用this.$route
	设置默认首页，为route文件排序的第一个

