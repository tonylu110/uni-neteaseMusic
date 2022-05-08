<template>
	<view class="content">
		<TitleBar :title="title" :navShadowColor="navShadowColor"></TitleBar>
		<scroll-view scroll-y="true" @scroll="scroll" enable-flex="true" :style="{height: screenHeight - systemBarHeight - rpx2px(100) + 'px'}">
			<view class="list_main">
				<view class="list_title">搜索</view>
				<view class="search_bar">
					<uni-search-bar bgColor="white" v-model="searchValue" @confirm="search" radius="20" cancelButton="none" />
				</view>
				<view class="his_word" v-if="hisShow">
					<view class="list_small_title">
						历史记录：
					</view>
					<view :scroll-y="true" class="allHW allHisW">
						<view class="hotword" v-for="(item,index) in keywords" :key="index" @click="keywordToSearch(item)">
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
						<view class="words" @click="keywordToSearch(item.first)">
							<text :style="{color: (index < 3 ? 'red' : 'black')}">{{ index + 1 }}</text> {{ item.first }}
						</view>
					</view>
				</view>
				<view class="list_small_title">
					热门电台：
				</view>
				<view class="hot_main">
					<view v-for="(item,index) in djHot" :key="index">
						<view class="words dj_words" @click="keywordToSearch(item.name)">
							<text :style="{color: (index < 3 ? 'red' : 'black')}">{{ index + 1 }}</text> {{ item.name }}
						</view>
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
				networkIsWorking: false,
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
			uni.getStorage({
				key: 'hotWords',
				success: (res) => {
					this.hotWords = res.data
				}
			})
			uni.getStorage({
				key: 'djHot',
				success: (res) => {
					this.djHot = res.data
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
						var oldhotWords
						uni.getStorage({
							key: 'hotWords',
							success: (res) => {
								oldhotWords = res.data
							}
						})
						uni.setStorage({
							key: 'hotWords',
							data: res.data.result.hots,
						})
						if (oldhotWords != res.data.result.hots) {
							this.hotWords = res.data.result.hots
						}
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
						var oldDjHot
						uni.getStorage({
							key: 'djHot',
							success: (res) => {
								oldDjHot = res.data
							}
						})
						uni.setStorage({
							key: 'djHot',
							data: res.data.djRadios,
						})
						if (oldDjHot != res.data.djRadios) {
							this.djHot = res.data.djRadios
						}
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
				uni.navigateTo({
					url: '../searchResult/searchResult?keyword=' + this.searchValue
				})
			},
			keywordToSearch(keyword) {
				var keyword = keyword
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
				uni.navigateTo({
					url: '../searchResult/searchResult?keyword=' + keyword
				})
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
			}
		}
	}
</script>

<style>
	@import url(./search.css);
</style>
