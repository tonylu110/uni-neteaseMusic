<template>
	<view class="content">
		<TitleBar :title="title" :navShadowColor="navShadowColor" :showBackButton="false"></TitleBar>
		<scroll-view scroll-y="true" @scroll="scroll" enable-flex="true" :style="{height: screenHeight - systemBarHeight - rpx2px(100) - 60 + 'px'}">
			<view @click="toSearch()" class="search_bar shearch_fixed" :style="{top: systemBarHeight + rpx2px(140) + 'px'}" v-show="!searbarShow">
				<uni-icons type="search" size="30" color="black"></uni-icons>
			</view>
			<view class="list_main" :style="{marginBottom: music.show ? '170rpx' : ''}">
				<view class="list_title">首页</view>
				<view @click="toSearch()" class="search_bar" v-show="searbarShow">
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
				<view class="list_small_title" v-show="networkIsWorking">
					推荐歌单：
				</view>
				<scroll-view :scroll-x="true" class="highquality_lists_scroll" v-show="networkIsWorking">
					<view class="highquality_lists">
						<view class="highquality_list" v-for="(item,index) in hightList" :key="index">
							<image :src="item.picUrl" mode=""></image>
							<view class="highquality_list_name">
								{{ item.name }}
							</view>
						</view>
					</view>
				</scroll-view>
				<view class="list_small_title" v-show="networkIsWorking">
					热门专辑：
				</view>
				<scroll-view :scroll-x="true" class="highquality_lists_scroll" v-show="networkIsWorking">
					<view class="highquality_lists">
						<view class="highquality_list" v-for="(item,index) in albumList" :key="index">
							<image :src="item.picUrl" mode=""></image>
							<view class="highquality_list_name">
								{{ item.name }}
							</view>
						</view>
					</view>
				</scroll-view>
				<view class="list_small_title" v-show="networkIsWorking">
					热门电台：
				</view>
				<scroll-view :scroll-x="true" class="highquality_lists_scroll" v-show="networkIsWorking">
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
		<view class="music_player" v-show="music.show">
			<lizhili-audio :src='music.url' ref='lizhili' :isPlay="music.isPlay" theme="white" :url='music.img' :name='music.name' :author='music.author' ></lizhili-audio>
		</view>
	</view>
</template>

<script>
	import lizhiliAudio from '@/components/lizhili-audio/lizhili-audio.vue'
	export default {
		components: {
			lizhiliAudio
		},
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
				searbarShow: true,
				networkIsWorking: false,
				music: {
					url: '',
					img: '',
					name: '',
					author: '',
					show: false,
					isPlay: false,
				},
				firstLoad: true
			}
		},
		onLoad() {
			this.setMusic(true)
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
		onShow() {
			this.setMusic(false)
		},
		methods: {
			setMusic(onload) {
				uni.getStorage({
					key: 'music',
					success: (res) => {
						if (onload) {
							this.music.url = res.data.url
							this.music.img = res.data.img
							this.music.name = res.data.name
							this.music.author = res.data.author
							this.music.show = res.data.show
							uni.setStorage({
								key: 'music',
								data: this.music
							})
						} else {
							if (!this.firstLoad) {
								this.music.isPlay = true
							}
							this.music = res.data
							this.firstLoad = false
						}
					}
				})
			},
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
						this.networkIsWorking = true
					}
				})
			},
			toSearch() {
				uni.navigateTo({
					url: '../search/search'
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
