<template>
	<view class="content">
		<view class="system_bar" :style="{height: systemBarHeight + 'px'}"></view>
		<view class="navigator_bar" :style="{boxShadow: '0px 0px 30px #000000' + navShadowColor}">
			{{ title }}
		</view>
		<scroll-view scroll-y="true" @scroll="scroll" enable-flex="true" :style="{height: screenHeight - systemBarHeight - rpx2px(100) - 60 + 'px'}">
			<view class="search_bar shearch_fixed" :style="{top: systemBarHeight + rpx2px(140) + 'px'}" v-show="!searbarShow">
				<uni-icons type="search" size="30" color="black"></uni-icons>
			</view>
			<view class="list_main">
				<view class="list_title">首页</view>
				<view class="search_bar" v-show="searbarShow">
					<uni-icons type="search" size="30" color="black"></uni-icons>
				</view>
				<view class="search_area" v-show="!searbarShow"></view>
				<swiper class="swiper-box" indicator-dots="true" autoplay="true" interval="3000">
					<swiper-item v-for="(item ,index) in bannerList" :key="index">
						<view class="swiper-item">
							<image :src="item.pic" mode="" class="swiper_img"></image>
						</view>
					</swiper-item>
				</swiper>
				<view class="list_small_title">
					推荐歌单：
				</view>
				<scroll-view :scroll-x="true" class="highquality_lists_scroll">
					<view class="highquality_lists">
						<view class="highquality_list" v-for="(item,index) in hightList" :key="index">
							<image :src="item.picUrl" mode=""></image>
							<view class="highquality_list_name">
								{{ item.name }}
							</view>
						</view>
					</view>
				</scroll-view>
				<view class="list_small_title">
					热门专辑：
				</view>
				<scroll-view :scroll-x="true" class="highquality_lists_scroll">
					<view class="highquality_lists">
						<view class="highquality_list" v-for="(item,index) in albumList" :key="index">
							<image :src="item.picUrl" mode=""></image>
							<view class="highquality_list_name">
								{{ item.name }}
							</view>
						</view>
					</view>
				</scroll-view>
				<view class="list_small_title">
					热门电台：
				</view>
				<scroll-view :scroll-x="true" class="highquality_lists_scroll">
					<view class="highquality_lists">
						<view class="highquality_list" v-for="(item,index) in djprogram" :key="index">
							<image :src="item.picUrl" mode=""></image>
							<view class="highquality_list_name">
								{{ item.name }}
							</view>
						</view>
					</view>
				</scroll-view>
			</view>
		</scroll-view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				getUrl: 'https://netease-cloud-music-api-eta-rust.vercel.app',
				title: '',
				systemBarHeight: 0,
				navShadowColor: '00',
				screenHeight: 0,
				hightList: [],
				bannerList: [],
				albumList: [],
				djprogram: [],
				searbarShow: true
			}
		},
		onLoad() {
			uni.getSystemInfo({
				success: (res) => {
					this.systemBarHeight = res.statusBarHeight
					this.screenHeight = res.screenHeight
				}
			})
			this.getHighQuality()
			this.getBanners()
			this.getAlbum()
			this.getDjprogram()
		},
		methods: {
			getHighQuality() {
				uni.request({
					url: this.getUrl + '/personalized',
					data: {
						limit: '10'
					},
					success: (res) => {
						this.hightList = res.data.result
					}
				})
			},
			getAlbum() {
				uni.request({
					url: this.getUrl + '/album/newest',
					success: (res) => {
						this.albumList = res.data.albums
					}
				})
			},
			getDjprogram() {
				uni.request({
					url: this.getUrl + '/personalized/djprogram',
					success: (res) => {
						this.djprogram = res.data.result
					}
				})
			},
			getBanners() {
				uni.request({
					url: this.getUrl + '/banner',
					data: {
						type: '2'
					},
					success: (res) => {
						this.bannerList = res.data.banners
					}
				})
			},
			scroll(e) {
				if (e.detail.scrollTop > 61) {
					this.title = '首页'
					this.searbarShow = false
				} else {
					this.title = ''
					this.searbarShow = true
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
			}
		}
	}
</script>

<style>
	@import url(./index.css);
</style>
