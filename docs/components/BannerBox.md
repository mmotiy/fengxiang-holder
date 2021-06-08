## 更多推荐组件
### 源码目录
>d-name/components/dio-bannerbox/dio-bannerbox.vue<br>
>旧版本：~~d-name/components/bannerbox/Bannerbox.vue~~ 不再使用

### 使用场景
1.用户测试完成，在结果页面下方，展示更多更多产品给用户<br>
2.产品首页除一屏以外的内容展示更多产品给用户

>tip:提高转化率，缩短用户测试其余产品的路劲
### 使用方式
凡是dio开头的组件，使用时不需要再`script`中再次引入。可以直接在`template`中使用。
```html
...
    <dio-bannerbox dictKey="TESTT_HOME_LIST1" ref="banner" @change="unBack()"></dio-bannerbox>
...
```
#### 属性说明

|属性名|类型|必填|说明|
|----|----|----|---|
|dictKey|字符串|是|字典中保存该产品推荐列表的字典key值|

#### 事件说明

|属性名|类型|必填|说明|
|----|----|----|---|
|change|回调|否|当用户点击推荐列表中的任何一款产品时，会触发该事件|

#### 方法说明

|方法名|说明|
|----|---|
|loadMoreTest|异步加载更多，如果该组件在加载中，则调用该方法不会再次触发加载|
|jumpMini|根据item的配置信息进行跳转|

### 源码解析
`TESTT_HOME_LIST1`对应的部分数据格式
```json
[{"name":"你和哪个明星同一天生日？","desc":"你和哪个明星同一天生日？","type":"reLaunch","icon":"http://create.adota.cn/quwan/26/iconn.png","path":"/pages/birthdayWithStar/test?dictKey=QUWAN_NHNGMXTYTSR","num":776879,"label":["明星"],"dictKey":"QUWAN_NHNGMXTYTSR_MODE","anotherName":"你和哪个明星同一天生日？","anotherDesc":""}]
```
接口`bottom-banner/v2/page`可以分页获取数组字典中保存的记录。适用于所有保存值为数组的字典。

```html
<template>
	<view class="stripe_bg">
		<view style="background-color: #FFFFFF;width: 750rpx;display: flex;flex-direction: column;align-items: center;">
			<image style="width: 280rpx;height: 62rpx;margin: 20rpx 0 16rpx 0;" src="../../static/bannerBoxTitle.png"></image>
			<image style="width: 274rpx;height: 26rpx;" src="../../static/bannerBoxDes.png"></image>
			<view class="banner-box" v-if="!item.isDel" @click="jumpMini(item)" v-for="(item, index) of items" :key="index">
				<view class="banner-bax-shadow"></view>
				<view class="banner-box-main">
					<view style="background-color: #FFFFFF;width: 100%;height: 97%;border-radius: 20rpx;display: flex;flex-direction: row;align-items: center;">
						<view class="goods_img">
							<image lazy-load="true" :src="item.icon" mode="aspectFill" style="width: 176rpx;height: 176rpx;"></image>
						</view>
						<view class="goods_font">
							<view class="title_font">{{ item.name || '---' }}</view>
							<view class="detail_font">{{ item.desc || '---' }}</view>
							<view style="margin-top: 22rpx;display: flex;align-items: center;flex-direction: row;height: 40rpx;margin-top: 20rpx;">
								<image style="width: 16rpx;height: 22rpx;flex-shrink: 0;" src="../../static/hot.png"></image>
								<view style="color: #7D728A;line-height: 24rpx;font-size: 24rpx;margin-left: 12rpx;flex-grow: 1;">{{ item.num }}人已玩</view>
								<image style="width: 178rpx;height: 52rpx;flex-shrink: 0;margin-right: 14rpx;" src="../../static/indexButton.png"></image>
							</view>
						</view>
					</view>
				</view>
			</view>
			<view style="display: flex;flex-direction: row;justify-content: center;">
				<!-- <view style=""></view> -->
				<view v-show="loading" class="preloader_1">
					<span></span>
					<span></span>
					<span></span>
					<span></span>
					<span></span>
				</view>
			</view>
			<view style="height: 100rpx;"></view>
		</view>
	</view>
</template>

<script>
import { config } from '../../common/lib/app.js';
export default {
	data() {
		return {
			items: [],
			totalPage: 0, //当前列表的总计页数
			loading: false,//是否在加载中
			page:0//当前加载到了第几页
		};
	},
	props: {
		dictKey: {
			type: String,
		}
	},
	mounted: function() {
		console.log('bannerBox mounted');
		this.loadMoreTest();
	},
	methods: {
		async loadMoreTest() {
			if (this.loading) {
				return;
			}
			this.loading = true;
			console.log(this.page, this.totalPage);
			if (this.page < this.totalPage || this.page == 0) {
				this.$dio.post('bottom-banner/v2/page', { key: this.dictKey, p: this.page, s: 10 }).then((data, index) => {
					if (data.data.status === 200) {
						setTimeout(_ => {
							if (this.page != 0) {
								this.items = this.items.concat(data.data.data.content);
							} else {
								this.items = data.data.data.content;
							}
							this.totalPage = data.data.data.totalPages;
							this.page++;
							this.loading = false;
						}, 800);
						console.log(this.items);
					} else {
						this.loading = false;
					}
				});
			} else {
				this.loading = false;
			}
		},
		jumpMini(item) {
			this.$emit('change')
			console.log(item.path);
			if (item.type === 'mini') {
				uni.navigateToMiniProgram({
					appId: item.appId,
					path: item.path,
					envVersion: item.envVersion,
					fail: console.error
				});
			} else if (item.type === 'inner') {
				uni.navigateTo({
					url: item.path
				});
			} else if (item.type === 'h5') {
				uni.navigateTo({
					url: item.path
				});
			} else if (item.type === 'reLaunch') {
				uni.reLaunch({
					url: item.path
				});
			}
		}
	}
};
</script>

<style>
/*... */
</style>
```