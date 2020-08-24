<template>
	<view class="container">
		<!-- loading动画 -->
		<mi-loading ref="loading" title="加载中" :hasMask="true"></mi-loading>
		<scroll-view>
			<view class="topBar">
				<image src="~@/static/img/banner.png" mode="widthFix" class="response"></image>
				<view class="searchBox" @click="getSearch">
					<view class="search-t">请输入关键字搜索</view>
					<view class="button">Go
						<view class="t"></view>
					</view>
				</view>
			</view>
			<!-- 轮播图 -->
			<swiper class="card-swiper" :class="dotStyle?'square-dot':'round-dot'" :indicator-dots="true" :circular="true"
			 :autoplay="true" interval="5000" duration="500" @change="cardSwiper" indicator-color="#8799a3"
			 indicator-active-color="#0081ff">
				<swiper-item v-for="(item,index) in swiperList" :key="index" :class="cardCur==index?'cur':''" @click="getBanner(item)">
					<view class="swiper-item">
						<image :src="item.url" mode="aspectFill" v-if="item.type=='image'"></image>
						<video :src="item.url" autoplay loop muted :show-play-btn="false" :controls="false" objectFit="cover" v-if="item.type=='video'"></video>
					</view>
				</swiper-item>
			</swiper>

			<!-- 电影列表 -->
			<view class="movieH">豆瓣高分</view>
			<view class="movieBox">
				<view v-for="(item, index) in movieInfo" :key="index" class="movieList" @click="getDate(item)">
					<image :src="item.cover" class="movieImg"></image>
					<view>
						{{ item.title | ellipsis }}
						<view class="moiveRate">
							<uni-rate disabled="true" :value="item.rate/2" size=14 active-color="#D81E06" color="#DADADA">
							</uni-rate>
							<text class="moiveRateT">{{item.rate}}</text>
						</view>
					</view>
				</view>
			</view>
			<uni-load-more :status="listStatus" :contentText="contentText" v-if="loadStatu"></uni-load-more>
		</scroll-view>


	</view>
</template>

<script>
	import uniList from "@/components/uni-list/uni-list.vue" // 宫格列表
	import uniListItem from "@/components/uni-list-item/uni-list-item.vue"
	import uniLoadMore from "@/components/uni-load-more/uni-load-more.vue" // 加载更多
	import miLoading from '../../components/mi-loading/mi-loading.vue' // loading动画
	let interstitialAd = null
	export default {
		components: {
			uniList,
			uniListItem,
			uniLoadMore,
			miLoading
		},
		filters: {
			// 名称超出显示省略号
			ellipsis(value) {
				if (!value) return '';
				if (value.length > 7) {
					return value.slice(0, 6) + '...'
				}
				return value
			}
		},
		onShareAppMessage: function(res) {
			var that = this;
			return {
				title: '老赵的杂货铺',
				path: 'pages/index/index' ,
				success: function(res) {
					console.log("转发成功:" + JSON.stringify(res));
					that.shareClick();
				},
				fail: function(res) {
					console.log("转发失败:" + JSON.stringify(res));
				}
			}
		},
		onReachBottom: function() { // 页面触底触发
			console.log('触底’')
			this.getmorenews()
		},
		onLoad() {
		   if (wx.createInterstitialAd) {
		     interstitialAd = wx.createInterstitialAd({
		       adUnitId: 'adunit-a795800b9c1da8c9'
		     })
		     interstitialAd.onLoad(() => {})
		     interstitialAd.onError((err) => {})
		     interstitialAd.onClose(() => {})
		   }
		   
		   // 在适合的场景显示插屏广告
		   if (interstitialAd) {
		     interstitialAd.show().catch((err) => {
		       console.error(err)
		     })
		   }
		  },
		mounted() {
			
			setTimeout(() => {
				this.$refs.loading.show() // 打开
			}, 10)
			uni.request({
				url: 'https://movie.douban.com/j/search_subjects',
				header: {
					"Content-Type": "application/text",
				},
				data: {
					type: 'movie',
					tag: '喜剧',
					sort: 'recommend',
					page_limit: 6,
					page_start: 0,
					playable: 'on'
				},
				success: (res) => {
					this.$refs.loading.hide() // 关闭
					console.log(res.data);
					this.movieInfo = res.data.subjects
					console.log(this.movieInfo, 'this.movieInfo ')
				}
			})
		},
		data() {
			return {
				contentText: {
					contentdown: "加载更多",
					contentrefresh: "正在加载...",
					contentnomore: "我是有底线的"
				},
				cardCur: 0, // 轮播图初始页面
				pageNum: 1, // 电影列表初始页数
				movieInfo: [], // 电影列表
				loadStatu: false, // loading是否显示
				listStatus: 'loading', // loading状态
				swiperList: [{ // 轮播图
					id: 30466885,
					type: 'image',
					url: '../../static/img/videoList/1.jpg',
					title: '我们永不言弃'
				}, {
					id: 30176393,
					type: 'image',
					url: '../../static/img/videoList/2.jpg',
					title: '误杀'
				}, {
					id: 27668250,
					type: 'image',
					url: '../../static/img/videoList/3.jpg',
					title: '南方车站的聚会'
				}, {
					id: 30198715,
					type: 'image',
					url: '../../static/img/videoList/4.jpg',
					title: '吹哨人'
				}],
				dotStyle: false, // 轮播图样式
				current: 0,
				mode: 'round',
				cuIconList: [{ // 宫格列表
					cuIcon: 'cardboardfill',
					color: 'red',
					badge: 0,
					name: 'uCharts'
				}, {
					cuIcon: 'recordfill',
					color: 'orange',
					badge: 0,
					name: '录像'
				}, {
					cuIcon: 'picfill',
					color: 'yellow',
					badge: 0,
					name: '图像'
				}, {
					cuIcon: 'noticefill',
					color: 'olive',
					badge: 0,
					name: '通知'
				}, {
					cuIcon: 'upstagefill',
					color: 'cyan',
					badge: 0,
					name: '排行榜'
				}, {
					cuIcon: 'clothesfill',
					color: 'blue',
					badge: 0,
					name: '皮肤'
				}]
			}
		},
		methods: {
			// 轮播图事件
			cardSwiper(e) {
				this.cardCur = e.detail.current
			},
			// 触底之后触发函数，
			getmorenews() {
				var that = this
				that.loadStatu = true
				if (that.movieInfo.length > 89) {
					that.listStatus = 'noMore'
					return
				}
				that.listStatus = 'loading'
				uni.request({
					url: 'https://movie.douban.com/j/search_subjects',
					method: 'GET',
					header: {
						"Content-Type": "application/text",
						"Cookie": 'bid=gmauOdTjPuM; ll="108296"; __utma=30149280.183190915.1588736614.1588736614.1588736614.1; __utmc=30149280; __utmz=30149280.1588736614.1.1.utmcsr=google|utmccn=(organic)|utmcmd=organic|utmctr=(not%20provided); __utma=223695111.1019919151.1588736616.1588736616.1588736616.1; __utmc=223695111; __utmz=223695111.1588736616.1.1.utmcsr=douban.com|utmccn=(referral)|utmcmd=referral|utmcct=/; _pk_ref.100001.4cf6=%5B%22%22%2C%22%22%2C1588736616%2C%22https%3A%2F%2Fwww.douban.com%2F%22%5D; _vwo_uuid_v2=DF41C6A193D708C4010E7A2D6F958507C|41887b5e2aa3fee81b7580e887d7e604; __yadk_uid=CaK93hfK60gv0yih8eYhMiiMaicvymVR; _pk_id.100001.4cf6=eb02d84691b41619.1588736616.1.1588736620.1588736616.; __gads=ID=5f256365a15a3bd0:T=1588736620:S=ALNI_MaoShIvp1GQXf-3uNudDn-YkC1ylA; dbcl2="199894991:L6mq4GWyH2I"; ck=C3EE'

					},
					data: {
						type: 'movie',
						tag: '喜剧',
						sort: 'recommend',
						page_limit: 6,
						page_start: that.pageNum * 6,
						playable: 'on'
					},
					success: function(res) {
						that.pageNum++;
						console.log(that.pageNum)
						console.log(res);
						that.movieInfo = that.movieInfo.concat(res.data.subjects);
						that.listStatus = 'loading'
					}
				})
			},
			getBanner(item) {
				uni.setStorage({
						key: 'storage_bg',
						data: 'https://img1.doubanio.com/view/photo/s_ratio_poster/public/p2559577569.jpg',
						success: function() {}
					}),
					uni.setStorage({
						key: 'storage_h1',
						data: item.title,
						success: function() {
							console.log('success');
						}
					})
				uni.navigateTo({
					url: '../detail/index?id=' + item.id,
					animationType: 'pop-in',
					animationDuration: 200
				})
			},
			getSearch() {
				uni.navigateTo({
					url: '../search/index',
					animationType: 'pop-in',
					animationDuration: 200
				})
			},
			getDate(item) {
				uni.setStorage({
					key: 'storage_bg',
					data: item.cover,
					success: function() {
						console.log('item.images.small');
					}
				})
				uni.setStorage({
					key: 'storage_h1',
					data: item.title,
					success: function() {
						console.log('success');
					}
				})
				uni.navigateTo({
					url: '../detail/index?id=' + item.id,
					animationType: 'pop-in',
					animationDuration: 200
				})
			},
			getChart(item) {
				console.log(item, 'item')
				uni.navigateTo({
					url: '../charts/index?type=' + item.name,
					animationType: 'pop-in',
					animationDuration: 200
				})
			}
		},
	}
</script>

<style scoped>
	/* 顶背景图 */
	.response {
		width: 100%;
	}

	.topBar {
		position: relative;
	}

	/* 搜索框 */
	.searchBox {
		position: absolute;
		left: 50%;
		margin-left: -30%;
		bottom: 10rpx;
		width: 60%;
		height: 60rpx;
		line-height: 60rpx;
		text-align: center;
		color: #3499cd;
		background-color: #ddd;
		border-radius: 10rpx;
		overflow: hidden;
		display: flex;
	}

	.button {
		width: 20%;
		position: relative;
		color: #FFFFFF;
		background-color: #6C7CFD;
	}

	.search-t {
		line-height: 60rpx;
		flex: 1;
	}

	.t {
		border-width: 12rpx;
		border-style: solid;
		border-color: transparent #6C7CFD transparent transparent;
		position: absolute;
		right: 100%;
		top: 18rpx;
	}

	/* 轮播图 */
	.swiper-item {
		width: 80%;
		;
		height: 100%;
		margin: 0 auto;
		border-radius: 20upx 20upx 0 0;
	}

	.swiper-img {
		width: 100%;
		height: 100%;
		border-radius: 20upx 20upx 0 0;
	}

	/* 电影列表 */
	@-webkit-keyframes masked-animation {
		0% {
			background-position: 0 0
		}

		to {
			background-position: -100% 0
		}
	}

	.movieH {
		width: 94%;
		margin: 0 auto;
		height: 100rpx;
		line-height: 100rpx;
		font-weight: 900;
		color: #DD524D;
		font-size: 36rpx;
		background-image: -webkit-linear-gradient(left, #02ab6c, #DD524D 25%, #3499cd 50%, #DD524D 75%, #3499cd);
		-webkit-text-fill-color: transparent;
		-webkit-background-clip: text;
		-webkit-background-size: 200% 100%;
		-webkit-animation: masked-animation 1s infinite linear;
	}

	.movieBox {
		width: 94%;
		margin: 0 auto;
		display: flex;
		justify-content: space-between;
		flex-wrap: wrap;
	}

	.movieList {
		/* height: 300rpx; */
		width: 30%;
	}

	.movieImg {
		height: 300rpx;
		border-radius: 15rpx;
	}

	.moiveRate {
		height: 40rpx;
		line-height: 40rpx;
		display: flex;
		justify-content: space-between;
		align-items: center;
		margin-bottom: 15rpx;
	}

	.moiveRateT {
		margin-right: 4rpx;
	}
</style>
