<script>
	export default {
		onLaunch: function() {
			console.log('App Launch')
		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		}
	}
</script>

<style>
	/*每个页面公共css */
	.music_player{
		position: fixed;
		bottom: 0;
		height: 200rpx;
		display: flex;
		flex-direction: row;
		align-items: center;
		justify-content: center;
	}
</style>
