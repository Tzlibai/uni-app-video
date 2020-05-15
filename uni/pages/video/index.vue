<template>
	<view>
		<!-- loading动画 -->
		<uni-popup ref="popup" type="bottom">
			<view style="height: 200rpx;background-color: #fff;display: flex;justify-content: center;align-items: center;">
				<text>From: zhaohongcheng.com</text>
			</view>
		</uni-popup>
		<mi-loading ref="loading" title="加载中" :hasMask="true"></mi-loading>
		<cu-custom bgColor="bg-gradual-blue" :isBack="true">
			<block slot="backText"></block>
			<block slot="content">{{ titleM }}</block>
		</cu-custom>
		<view>
			<uni-fab :content="content" :horizontal="horizontal" :vertical="vertical" :direction="direction" @trigger="trigger"></uni-fab>
		</view>
		<view v-if="playInfo">
			<iframe id="player" :src="'https://cdn.yangju.vip/k/?url=' + type" frameborder="0" width="100%" height="100%"
			 allowTransparency="true" scrolling="no" lign="middle" hspace="0" vspace="0" autoPlay=true allowfullscreen="true"
			 marginheight="0" marginwidth="0" name="tv"></iframe>
		</view>
		<view style="width: 94%;margin: 0 auto;margin-top: 30rpx;text-align: center;">视频加载完成后，请您手动关闭上方广告</view>
		<view style="width: 94%;margin: 0 auto;margin-top: 30rpx;text-align: center;">
			<text>提示:如遇加载失败，请点击左下角刷新按钮</text></view>
		<view style="width: 94%;margin: 0 auto;margin-top: 550rpx;text-align: center;color: #CCCCCC;">
			zhaohongcheng.com
			<br>
			声明本软件仅供测试交流，请勿用于商业用途! 
			<br>
			如果侵犯到您的权益，请联系zhaohongcheng5@163.com
			</view>
	</view>
</template>

<script>
	import miLoading from '../../components/mi-loading/mi-loading.vue' // loading动画
	import uniFab from '@/components/uni-fab/uni-fab.vue';
	import uniPopup from '@/components/uni-popup/uni-popup.vue' // 弹出层
	export default {
		components: {
			uniFab,miLoading, uniPopup
		},
		onLoad(option) {
			console.log(option)
			this.type = option.type
			var that = this
			// 获取本地存储的图片
			uni.getStorage({
				key: 'title',
				success: function(res) {
					that.titleM = res.data
				}
			});
		},
		data() {
			return {
				webviewStyles: {
					progress: {
						color: 'red'
					},
					height: '70%'
				},
				type: '', //电影连接
				playInfo: true, //电影播放
				titleM: '',
				title: 'uni-fab',
				horizontal: 'left',
				vertical: 'bottom',
				direction: 'horizontal',
				pattern: {
					color: '#7A7E83',
					backgroundColor: '#fff',
					selectedColor: '#007AFF',
					buttonColor: '#007AFF'
				},
				content: [{
						iconPath: '/static/img/reset.png',
						selectedIconPath: '/static/img/reset.png',
						text: '刷新',
						id: 1,
						active: false
					},
					{
						iconPath: '/static/img/help.png',
						selectedIconPath: '/static/img/help.png',
						text: '	关于',
						id: 2,
						active: false
					}
				]
			}
		},
		methods: {
			trigger(item) {
				var that = this
				console.log(item.item.id)
				if(item.item.id === 1){
					that.playInfo = false
					setTimeout(() => {
						that.$refs.loading.show() // 打开
					}, 10)
					setTimeout(() =>{
						that.playInfo = true
						that.$refs.loading.hide() // 打开
					},1000)
				}else {
					that.$refs.popup.open()
				}
				console.log(index)
			}
		},
	}
</script>

<style>
	.videoUrl {
		height: 90%;
	}
</style>
