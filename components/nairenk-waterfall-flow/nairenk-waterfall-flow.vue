<template>
	<view class="waterfall-flow" ref="waterfall">
		<!-- class="item" 必须有, 限制好 item 宽度  -->
		<view class="item" :style="item.style" v-for="(item, index) in newList" :key="item.key" :data-index="index" @click="choose">
			<!-- 盒子样式，自定义 -->
			<view class="box">
				<view class="pic">
					<image class="image" mode="widthFix" :src="item.image" style="width: 100%; display: block;" ></image>
				</view>
				<view class="content">{{item.content}}</view>
				<view class="user">
					<image style="width: 40upx; height: 40upx; border-radius: 50%; margin-right: 10upx;" 
						:src="item.user.avatar"></image>
					<text>{{item.user.name}}</text>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		props: {
			list: {
				type: Array,
				default() {
					return []
				}
			}
		},
		data () {
			return {
				newList: [], // 兼容小程序
				boxHeight: []
			}
		},
		watch: {
			list: function (newVal, oldVal) {
				if (newVal != oldVal) {
					// #ifndef MP-WEIXIN
					this.newList = this.list;
					// #endif
					// #ifdef MP-WEIXIN
					this.newList = this.newList.concat(newVal)
					// #endif
					let mark = oldVal.length;
					let len = this.list.length;
					let screenWidth = uni.getSystemInfoSync().screenWidth;
					let style = '';
					this.$nextTick(async function () {
						const query = uni.createSelectorQuery().in(this);
						let dataArray = [];
						await new Promise((resolve, reject)=>{
							setTimeout(() => {
								query.selectAll('.waterfall-flow .item').fields({size: true}, data => {
									dataArray = data;
									resolve()
								}).exec();
							}, 520) // 针对图片 mode="widthFix" 尺寸改变时的延迟
						})
						for (let i = mark; i < len; i++) {
							if (i < 2) {
								style = `top: 0; left: ${(screenWidth/2) * i}px;`
								this.boxHeight.push(dataArray[i].height)
							} else {
								let minHeight = this.boxHeight[0];
								if (minHeight > this.boxHeight[1]) {
									minHeight = this.boxHeight[1];
									this.boxHeight[1] = minHeight + dataArray[i].height;
									style = `top: ${minHeight}px; left: ${(screenWidth/2)}px;`
								} else {
									this.boxHeight[0] = minHeight + dataArray[i].height;
									style = `top: ${minHeight}px; left: 0;`
								}
							}
							this.$set(this.newList[i], 'style', style);
							this.$forceUpdate();
						}
					})
				}
			}
		},
		methods: {
			// 选中
			choose(e) {
				let index = e.currentTarget.dataset.index;
				this.$emit('click', this.newList[index]);
			}
		}
	}
</script>

<style scoped>
	image {
		display: block;
		will-change: transform;
	}
	.waterfall-flow {
		position: relative;
		background-color: #F4F4F4;
	}
	/* 为了初始化，还为定位上，加上这个 */
	.item {
		width: 375rpx;
		position: absolute;
		top: -375rpx;
		left: -375rpx;
	}
	.box {
		margin: 10rpx;
		box-shadow: 0 0 8rpx #ccc;
		overflow: hidden;
	}
	.user {
		display: flex;
		align-items: center;
		padding: 10rpx;
	}
	.content {
		font-size: 32rpx;
		padding: 20rpx 10rpx 0 10rpx;
		color: #333;
	}
</style>
