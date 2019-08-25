# 基于 uni-app 实现的瀑布流组件

对于 image 组件里的 widthFix 模式，会有一个再次加载的问题，会使标签的高度获取不精准，使定位出现偏差。

如果想使用 widthFix 这个模式，最好是后台在回数据的时候，也把图片的尺寸加上，然后通过 :style 给上高度。

## 兼容平台

H5、APP、小程序。

# @click 事件

触发 @click 事件会回调选中的 JSON 数据，可进行下一步操作。

# HTML 中使用

<template>
    <view class="app">
        <waterfall-flow :list="list" :loading="loading" @click="choose"></waterfall-flow>
    </view>
</template>

```javascript

<script>
	// 瀑布流组件
	import WaterfallFlow from '../../components/nairenk-waterfall-flow/nairenk-waterfall-flow.vue';
	// 模拟 JSON 数据
	const data = require('@/common/json/data.json');
	export default {
		data() {
			return {
				page: 1,
				start: 0,
				end: 0,
				list: [], // 列表
				loading: true
			}
		},
		onLoad() {
			this.getList();
		}, 
		onReachBottom() {
			this.page++;
			this.loading = true;
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
					setTimeout(() => {
						this.end = this.page * 10;
						this.list = this.list.concat(data.list.slice(this.start, this.end));
						this.start = this.end;
						// 延迟 120 毫秒隐藏加载动画，为了跟组件里面的 100 毫秒定位有个平缓过度
						setTimeout(() => {
							this.loading = false;
						}, 120);
					}, 1000)
				} else {
					this.loading = false;
				}
			}
		},
		components: {
			WaterfallFlow
		}
	}
</script>
```

注 ：如果出现页面错乱，不要使用 widthFix 模式。

