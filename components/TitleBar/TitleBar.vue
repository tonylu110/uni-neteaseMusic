<template>
	<view>
		<view class="system_bar" :style="{height: systemBarHeight + 'px'}"></view>
		<view class="navigator_bar" :style="{boxShadow: '0px 0px 30px #000000' + navShadowColor}">
			<uni-icons type="arrow-left" size="30" @click="back()" v-if="showBackButton"></uni-icons>
			<view class="" style="width: 30px;" v-if="!showBackButton"></view>
			{{ title }}
			<view class="" style="width: 30px;"></view>
		</view>
	</view>
</template>

<script>
	export default {
		name:"TitleBar",
		props: {
			showBackButton: {
				type: Boolean,
				default: true
			},
			title: {
				type: String,
				default: '标题'
			},
			navShadowColor: {
				type:String,
				default: '00'
			}
		},
		data() {
			return {
				systemBarHeight: 0
			};
		},
		mounted() {
			uni.getSystemInfo({
				success: (res) => {
					this.systemBarHeight = res.statusBarHeight
				}
			})
		},
		methods: {
			back() {
				uni.navigateBack({
					
				})
			}
		}
	}
</script>

<style>
	.system_bar{
		position: relative;
		width: 750rpx;
		background-color: white;
		z-index: 1;
		background-color: #f8f8f8;
	}
	.navigator_bar{
		width: 650rpx;
		height: 100rpx;
		background-color: white;
		font-size: 35rpx;
		font-weight: bold;
		background-color: #f8f8f8;
		display: flex;
		flex-direction: row;
		align-items: center;
		padding: 0px 50rpx 0px 50rpx; 
		justify-content: space-between;
	}
</style>