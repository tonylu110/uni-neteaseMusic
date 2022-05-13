<template>
		<view :class="theme">
			<view class="left">
				<image :src="url" mode="widthFix"></image>
				<text v-if="!is_fang" class="my-icons  btn" @tap="bo">&#xe7b0;</text>
				<text v-else class="my-icons btn dong" @tap="ting">&#xe502;</text>
			</view>
			<view class="center">
				<view class="info">
					<view class="title">
						{{name}}
					</view>
					<view class="desc">
						{{author}}
					</view>
				</view>
				<view class="time">
					{{time_str}}
				</view>
			</view>
		</view>
</template>

<script>
export default {
	name  : "lizhili-audio",
	props : {
		url : { type : String,  default : 'https://bjetxgzv.cdn.bspapp.com/VKCEYUGU-uni-app-doc/7fbf26a0-4f4a-11eb-b680-7980c8a877b8.png'},
		name :{ type : String,  default : '暂无标题'},
		author :{ type : String,  default : '佚名'},
		src:{ type : String,  default : null},
		theme:{ type : String,  default : 'black'},
		heigh:{ type : Number,  default : 105},
	},
	data() {
		return {
			is_pause:false,
			is_fang:false,
			time:0,
			time_str:'00:00',
			intervalID:null,
			innerAudioContext:null
		};
	},
	watch: {
	    time(newName, oldName) {
			let fen=Math.floor(newName/60);
			let miao=newName%60;
			this.time_str=(fen >=10 ? fen : '0'+fen)+':'+(miao >=10 ? miao : '0'+miao)
	    }
	},
	created() {
		this.innerAudioContext=uni.createInnerAudioContext();
		this.innerAudioContext.onPlay(() => {
			this.time = 0
			if(!this.intervalID){
				this.intervalID=setInterval(()=>{
					if(!this.is_pause){
						this.time++
					}
				},1000)
			}
			this.is_pause=false
			this.is_fang=true
		});
		this.innerAudioContext.onCanplay((e) => {
			//console.log(this.innerAudioContext.duration);
		});
		this.innerAudioContext.onError((res) => {
			console.log('不能播放');
			console.log(res.errMsg);
			console.log(res.errCode);
			clearInterval(this.intervalID)
			this.is_fang=false
			this.is_pause=false
			this.time=0
			this.time_str='00:00'
		});
		this.innerAudioContext.onPause(() => {
			this.is_pause=true
			this.is_fang=false
		});
		this.innerAudioContext.onEnded(() => {
			console.log('播放结束');
			clearInterval(this.intervalID)
			this.is_fang=false
			this.is_pause=false
			this.time=0
			this.time_str='00:00'
		});
	},
	methods: {
		bo(){
			console.log('开始播放');
			this.innerAudioContext.src=this.src
			this.innerAudioContext.play()
		},
		ting(){
			console.log('暂停播放');
			this.innerAudioContext.pause()
		},
		stop(){
			console.log(777);
			this.innerAudioContext.stop()
		}
	}
};
</script>

<style lang="scss">
@font-face {
    font-family: 'iconfont';
    src: url('data:font/woff;charset=utf-8;base64,d09GRgABAAAAAAT8AA0AAAAAB7gAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAABGRlRNAAAE4AAAABoAAAAckHri00dERUYAAATAAAAAHgAAAB4AKQALT1MvMgAAAaAAAABGAAAAYDs3TA1jbWFwAAAB/AAAAEUAAAFK6nPmE2dhc3AAAAS4AAAACAAAAAj//wADZ2x5ZgAAAlAAAADrAAABhNMYjdNoZWFkAAABMAAAAC8AAAA2H2//x2hoZWEAAAFgAAAAHgAAACQJWAaiaG10eAAAAegAAAAUAAAAFBBxAYxsb2NhAAACRAAAAAwAAAAMAG4Awm1heHAAAAGAAAAAHwAAACABEwBUbmFtZQAAAzwAAAFGAAACgl6CAQJwb3N0AAAEhAAAADIAAABHQwnvTXjaY2BkYGAA4qcBBprx/DZfGbhZGEDgbnbsUwT9v4G1irkByOVgYAKJAgAqCQreAHjaY2BkYGBu+N/AEMMuwwAErFUMjAyogBUAUzcDCAAAeNpjYGRgYGBl8GBgYQABJiDmAkIGhv9gPgMAEDUBaAB42mNgYe1jnMDAysDA1Ml0hoGBoR9CM75mMGLkAIoysDIzYAUBaa4pDAeeMj3fwNzwv4GBgfkOA5BkYERSosDACACqwA3AAAAEAAAAAAAAAAFVAAAHHAGMBAAAAHjaY2BgYGaAYBkGRgYQcAHyGMF8FgYNIM0GpBkZmJ4yPd/w/z8DA4SWZpQIgaoHAkY2BjiHkQlIMDGgAkaGYQ8AYTYKUAAAAAAAAAAAAAAAAG4AwnjaY2Bh7PnfyVrFXM4gwaDLYMXgzsDAqMTOx8jOJiIuxyguZmRux2huZqKux6iuxqykrqauZmJuZm5mJC4mLibCzsbOxqzEJiooImZkZipoomaMzGFuTouL6lbT0FDrjoo7g2CmlaUlW9rYWCanvUlLsrKxsUpKuyssLa0mLb0HQjGXo2uBMP+Eomh6AzKGqVla+N8+oD5hRicQiWAzMDAzMPxvYGFgbgD7zYBkn3EyMjFkxMf0qWtoqPfFxF9AMDOqMlJtbG1tUjO+wBi/GBv+NTA3oKuEMP84oKgFM5gO/Gf4zwAEAMMoYQMAeNp9kM1Kw0AUhc/0T21BxILrWRVBSH+WpbtC3blwUddtOklbkkyYTAtdunXlA7j1MXwAn0Fw5YN4Gq8IFZqQyzfn3nNmJgAu8QmFn6eNa2GFU9wJV3CCWLhK/VG4Rn4RrqOFN+EG9Q/hJm7USLiFtnpmgqqdcdUp0/ascIGRcAXneBCuUrfCNfKTcB1XeBVuUH8XbmKKL+EWOmqJMRwMZvCsC2jMsWNdIWRuhqisHhg7M/Nmoec7vQptFtmM4r+pv9Y942JskDDacWniTTJzRy1HWlOmORQc2bc0+gjQo2xcsbKZ7ge9o/Zb2rMy4vCeBbY85oCqp1Hzc7SnpInEGF4hIWvkZW9NJaQeMNZkxv3+lWIbD7yPdORsqifc1iSJ1bmzaxN6Di/LPXIM0eUbHaQH5eFTjnmfD7vdSAKC0Kb4Bg9qcIcAAHjaY2BigAAuBuyAFYgZGZgYmRiZhasS80oy89JBuCojUzc5v6CSLSk/LTEvHQCWzgqCAAAAAAAB//8AAgABAAAADAAAABYAAAACAAEAAwAEAAEABAAAAAIAAAAAeNpjYGBgZACCq0vUOUD03ezYpzAaAEFDBr4AAA==') format('woff');
    font-weight: normal;
    font-style: normal;
    font-display: swap;
}

.my-icons {
	font-family: 'iconfont' !important;
	font-size: 36px;
	font-style: normal;
	color: #FFFFFF;
	-webkit-font-smoothing: antialiased;
	-moz-osx-font-smoothing: grayscale;
}
@keyframes changeright{
	0%{transform:rotate(0deg);}
	50%{transform:rotate(180deg);}
	100%{transform:rotate(360deg);}
}

.dong{
	animation:changeright 6s linear infinite;
}
.black{
	display: flex;
	overflow: hidden;
	border-radius: 10rpx;
	.left{
		width: 10%;
		background-color: #172239;
		padding: 15rpx;
		position: relative;
		display: flex;
		justify-content: center;
		align-items: center;
		image{
			width: 100%;
			border-radius: 50%;
		}
		.btn{
			position: absolute;
			font-size: 40rpx;
		}
	}
	.center{
		justify-content: space-between;
		width: 90%;
		background-color: #525A6F;
		color: #FFFFFF;
		padding: 10rpx 20rpx;
		display: flex;
		flex-direction: row;
		.info{
			margin: 0 20rpx;
			.title{
				font-size: 28rpx;
			}
			.desc{
				margin-top: 15rpx;
				font-size: 24rpx;
			}
		}
		.time{
			display: flex;
			align-items: center;
			margin-left: 30rpx;
		}
	}
}
.white{
	display: flex;
	overflow: hidden;
	border-radius: 20rpx;
	width: 690rpx;
	height: 150rpx;
	box-shadow: 0px 0px 50px #00000030;
	.left{
		width: 150rpx;
		background-color: #f5f5f5;
		padding: 15rpx;
		position: relative;
		display: flex;
		justify-content: center;
		align-items: center;
		image{
			width: 100%;
			border-radius: 50%;
		}
		.btn{
			position: absolute;
			font-size: 40rpx;
		}
	}
	.center{
		justify-content: space-between;
		width: 90%;
		background-color: white;
		color: #333333;
		padding: 10rpx 20rpx;
		display: flex;
		flex-direction: row;
		.info{
			margin: 0 20rpx;
			
			.title{
				font-size: 28rpx;
			}
			.desc{
				margin-top: 15rpx;
				font-size: 24rpx;
			}
		}
		.time{
			display: flex;
			align-items: center;
			margin-left: 30rpx;
		}
	}
}
</style>
