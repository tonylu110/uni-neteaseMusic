<template>
	<view class="content">
		<TitleBar :showBackButton="false" :title="title" :navShadowColor="navShadowColor"></TitleBar>
		<scroll-view scroll-y="true" @scroll="scroll" :style="{height: screenHeight - systemBarHeight - rpx2px(100) - 60 + 'px'}">
			<view class="list_main">
				<view class="list_title">分类</view>
				<view class="list_small_title" v-show="networkIsWorking">
					歌单分类：
				</view>
				<view class="cate_list_main" v-show="networkIsWorking">
					<view class="cate_list" v-for="(item,index) in catList" :key="index">
						{{ item.name }}
					</view>
				</view>
				<view class="list_small_title" v-show="networkIsWorking">
					电台分类：
				</view>
				<view class="cate_list_main" v-show="networkIsWorking">
					<view class="cate_list dj_cate_list" v-for="(item,index) in djList" :key="index">
						<image :src="item.pic84x84IdUrl" mode=""></image>  {{ item.name }}
					</view>
				</view>
			</view>
		</scroll-view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				title: '',
				systemBarHeight: 0,
				navShadowColor: '00',
				screenHeight: 0,
				catList: [],
				djList: [],
				networkIsWorking: false
			}
		},
		onLoad() {
			uni.getSystemInfo({
				success: (res) => {
					this.systemBarHeight = res.statusBarHeight
					this.screenHeight = res.screenHeight
				}
			})
			this.getCatlist() 
			this.getDjList()
		},
		methods: {
			getDjList() {
				uni.request({
					url: 'https://netease-cloud-music-api-eta-rust.vercel.app/dj/catelist',
					success: (res) => {
						this.djList = res.data.categories
						this.networkIsWorking = true
					}
				})
			},
			getCatlist() {
				uni.request({
					url: 'https://netease-cloud-music-api-eta-rust.vercel.app/playlist/hot',
					success: (res) => {
						this.catList = res.data.tags
					}
				})
			},
			scroll(e) {
				if (e.detail.scrollTop > 61) {
					this.title = '分类'
				} else {
					this.title = ''
				}
				if (e.detail.scrollTop > 0) {
					this.navShadowColor = '15'
				} else {
					this.navShadowColor = '00'
				}
			},
			rpx2px(rpx) {
				return uni.upx2px(rpx)
			},
			px2rpx(px) {
				return px/(uni.upx2px(100)/100)
			},
		}
	}
</script>

<style>
	@import url(./cate.css);
</style>
