<template>
	<view view class="content">
		<TitleBar :title="title" :navShadowColor="navShadowColor"></TitleBar>
		<scroll-view scroll-y="true" @scroll="scroll" @scrolltolower="scrolltolower" enable-flex="true" :style="{height: screenHeight - systemBarHeight - rpx2px(100) + 'px'}">
			<view class="list_main">
				<view class="list_title">搜索：{{ searchKeyword }}</view>
				<view class="music_list">
					<view v-for="(item,index) in searchList" :key="index" class="music">
						<image :src="item.al.picUrl" mode=""></image>
						<view class="song_info">
							<view class="song_name">
								{{ item.name }}
							</view>
							<view class="song_al_name">
								{{ item.al.name }}<br/>
								演唱：
								<text v-for="(item,index) in item.ar" :key="index" class="ar_name">
									{{ item.name + ' '}}
								</text>
							</view>
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
				searchKeyword: '',
				searchList: [],
				page: 1
			}
		},
		onLoad(res) {
			uni.getSystemInfo({
				success: (res) => {
					this.systemBarHeight = res.statusBarHeight
					this.screenHeight = res.screenHeight
				}
			})
			this.searchKeyword = res.keyword
			this.getSearchResult()
		},
		methods: {
			scrolltolower(e) {
				this.page = this.page + 1
				this.getSearchResult()
			},
			getSearchResult() {
				uni.request({
					url: 'https://netease-cloud-music-api-eta-rust.vercel.app/cloudsearch',
					data: {
						keywords: this.searchKeyword,
						limit: '10',
						offset: (this.page-1)*10
					},
					success: (res) => {
						this.searchList = [...this.searchList, ...res.data.result.songs]
					}
				})
			},
			scroll(e) {
				if (e.detail.scrollTop > 61) {
					this.title = this.searchKeyword
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
	@import url(./searchResult.css);
</style>
