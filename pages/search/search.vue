<template>
	<view class="content">
		<view class="system_bar" :style="{height: systemBarHeight + 'px'}"></view>
		<view class="navigator_bar" :style="{boxShadow: '0px 0px 30px #000000' + navShadowColor}">
			<uni-icons type="arrow-left" size="30" @click="back()"></uni-icons>
			{{ title }}
			<view class="" style="width: 30px;"></view>
		</view>
		<scroll-view scroll-y="true" @scroll="scroll" enable-flex="true" :style="{height: screenHeight - systemBarHeight - rpx2px(100) + 'px'}">
			<view class="list_main">
				<view class="list_title">搜索</view>
			</view>
			<view class="search_bar">
				<uni-search-bar bgColor="white" v-model="searchValue" @confirm="search" radius="20" cancelButton="none" />
			</view>
			<view class="his_word" v-if="hisShow">
				<view class="list_small_title">
					历史记录：
				</view>
				<view :scroll-y="true" class="allHW allHisW">
					<view class="hotword" v-for="(item,index) in keywords" :key="index">
						{{ item }}
					</view>
				</view>
				<view class="clear_btn" type="default" @click="clearHisword"><uni-icons type="trash" size="30" color="#aaa"></uni-icons>清除搜索历史</view>
			</view>
			<view class="list_small_title">
				热搜榜：
			</view>
			<view class="hot_main">
				<view v-for="(item,index) in hotWords" :key="index">
					<view class="words">
						<text :style="{color: (index < 3 ? 'red' : 'black')}">{{ index + 1 }}</text> {{ item.first }}
					</view>
				</view>
			</view>
			<view class="list_small_title">
				热门电台：
			</view>
			<view class="hot_main">
				<view v-for="(item,index) in djHot" :key="index">
					<view class="words dj_words">
						<text :style="{color: (index < 3 ? 'red' : 'black')}">{{ index + 1 }}</text> {{ item.name }}
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
				keywords: [],
				searchValue: '',
				hisShow: false,
				hotWords: [],
				djHot: []
			}
		},
		onLoad() {
			uni.getSystemInfo({
				success: (res) => {
					this.systemBarHeight = res.statusBarHeight
					this.screenHeight = res.screenHeight
				}
			})
			uni.getStorage({
				key: 'keywords',
				success: (res) => {
					this.keywords = res.data
				}
			});
			uni.getStorage({
				key: 'hisWordShow',
				success: (res) => {
					this.hisShow = res.data
				}
			})
			this.getHodWords()
			this.getDjHot()
		},
		methods: {
			getHodWords() {
				uni.request({
					url: 'https://netease-cloud-music-api-eta-rust.vercel.app/search/hot',
					success: (res) => {
						this.hotWords = res.data.result.hots
					}
				})
			},
			getDjHot() {
				uni.request({
					url: 'https://netease-cloud-music-api-eta-rust.vercel.app/dj/hot',
					data: {
						limit: '5'
					},
					success: (res) => {
						this.djHot = res.data.djRadios
					}
				})
			},
			search() {
				var keyword = this.searchValue
				var keywords = this.keywords
				if (!keywords.includes(keyword)) {
					this.keywords = [keyword, ...keywords]
				} else {
					keywords = keywords.filter(item => item != keyword)
					this.keywords = [keyword, ...keywords]
				}
				uni.setStorage({
					key: 'keywords',
					data: this.keywords,
				})
				uni.setStorage({
					key: 'hisWordShow',
					data: true
				})
				this.hisShow = true
			},
			clearHisword() {
				uni.removeStorage({
					key: 'keywords',
				})
				this.hisShow = false
				this.keywords = []
				uni.setStorage({
					key: 'hisWordShow',
					data: false
				})
			},
			scroll(e) {
				if (e.detail.scrollTop > 61) {
					this.title = '搜索'
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
			},
			back() {
				uni.navigateBack({
					
				})
			}
		}
	}
</script>

<style>
	@import url(./search.css);
</style>
