<template>
	<view view class="content">
		<TitleBar :title="title" :navShadowColor="navShadowColor"></TitleBar>
		<scroll-view scroll-y="true" @scroll="scroll" @scrolltolower="scrolltolower" enable-flex="true" :style="{height: screenHeight - systemBarHeight - rpx2px(100) + 'px'}">
			<view class="list_main" :style="{marginBottom: music.show ? '200rpx' : ''}">
				<view class="list_title">搜索：{{ searchKeyword }}</view>
				<view class="music_list">
					<view v-for="(item,index) in searchList" :key="index" class="music" @click="playMusic(item.id, item.al.picUrl, item.name, item.ar)">
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
				title: '',
				systemBarHeight: 0,
				navShadowColor: '00',
				screenHeight: 0,
				searchKeyword: '',
				searchList: [],
				page: 1,
				music: {
					url: '',
					img: '',
					name: '',
					author: '',
					show: false,
					isPlay: false,
				},
				innerAudioContext: null
			}
		},
		onLoad(res) {
			uni.getSystemInfo({
				success: (res) => {
					this.systemBarHeight = res.statusBarHeight
					this.screenHeight = res.screenHeight
				}
			})
			uni.getStorage({
				key: 'music',
				success: (res) => {
					this.music = res.data
				}
			})
			this.searchKeyword = res.keyword
			this.getSearchResult()
			uni.$on('innerAudioContext',(data)=>{
				this.innerAudioContext = data
			})
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
			playMusic(id, img, name, author) {
				this.music.show = true
				this.music.isPlay = true
				this.music.url = 'https://music.163.com/song/media/outer/url?id=' + id + '.mp3'
				this.music.img = img
				this.music.name = name
				if (this.music.show) {
					this.music.author = ''
					this.innerAudioContext.onEnded()
				}
				if (this.music.author == '') {
					for (var i = 0; i < author.length; i++) {
						this.music.author = this.music.author + author[i].name + ' '
					}
				}
				uni.setStorage({
					key: 'music',
					data: this.music,
				})
				setTimeout(() => {
					this.$refs.lizhili.bo()
				}, 1)
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
