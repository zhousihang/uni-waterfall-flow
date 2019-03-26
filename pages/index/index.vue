<template>
	<view class="app">
		<waterfall-flow :list="list"></waterfall-flow>
	</view>
</template>

<script>
	// 瀑布流组件
	import WaterfallFlow from '@/components/waterfall-flow.vue';
	// 模拟 JSON 数据
	var data = require('@/common/json/data.json');
	export default {
		data() {
			return {
				page: 1,
				start: 0,
				end: 0,
				list: [] // 列表
			}
		},
		mounted() {
			this.getList();
		},
		// 触底加载更多
		onReachBottom() {
			this.page++;
			this.getList();
		},
		methods: {
			// 模拟数据加载
			getList() {
				if (this.list.length < data.list.length) {
					uni.showLoading({
						title: '加载中'
					});
					setTimeout(() => {
						this.end = this.page * 10;
						this.list = this.list.concat(data.list.slice(this.start, this.end));
						this.start = this.end;
						uni.hideLoading();
					}, 300)
				}
			}
		},
		components: {
			WaterfallFlow
		}
	}
</script>

<style>
	.app {
		height: 100%;
	}
</style>