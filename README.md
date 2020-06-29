# 基于 uni-app 实现的瀑布流组件
如果固定好图片尺寸的话，就可以把 setTimeout 时间调掉，setTimeout 延迟时间是兼容页面渲染太卡，导致的定位不准问题。
或者图片 mode 不要使用 widthFix，定时器是针对图片 mode="widthFix" 尺寸改变时的延迟。

## 兼容平台

H5、APP、小程序（测试了微信小程序）

# @click 事件

触发 @click 事件会回调选中的 JSON 数据，可进行下一步操作。

# HTML 中使用

<template>
    <view class="app">
        <waterfall-flow :list="list" @click="choose"></waterfall-flow>
    </view>
</template>

```javascript

<script>
	// 瀑布流组件
	import WaterfallFlow from '@/components/nairenk-waterfall-flow/nairenk-waterfall-flow.vue';
	// 模拟 JSON 数据
	const data = require('@/common/json/data.json');
	export default {
		components: {
			WaterfallFlow
		},
		data() {
			return {
				page: 1,
				start: 0,
				end: 0,
				list: [], // 列表
			}
		},
		onLoad() {
			this.getList();
		},
		onReachBottom() {
			this.page++;
			this.getList();
		},
		methods: {
			// 选中
			choose(item) {
				// item 返回选中 JSON 对象
				console.log(item)
			},
			// 模拟加载数据
			getList() {
				if (this.list.length < data.list.length) {
					uni.showLoading({
						title: '加载中...'
					})
					setTimeout(() => {
						this.end = this.page * 10;
						this.list = this.list.concat(data.list.slice(this.start, this.end));
						this.start = this.end;
						uni.hideLoading();
					}, 1000)
				}
			}
		}
	}
</script>
```

