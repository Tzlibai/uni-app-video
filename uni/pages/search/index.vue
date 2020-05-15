<template>
	<view class="container">
		<!-- loading动画 -->
		<mi-loading ref="loading" title="加载中" :hasMask="true"></mi-loading>
		<view style="background-color: #CAF2F2">
			<cu-custom :isBack="true">
				<block slot="backText"> </block>
				<block slot="content" class="tarbar">搜索</block>
			</cu-custom>
		</view>
		<scroll-view>
			<view class="topBar">
				<image src="~@/static/img/search.jpeg" mode="widthFix" class="response"></image>
				<view class="searchBox">
					<input class="search-t" placeholder="请输入关键字搜索" @input="messagesearch"></input>
					<view class="button" @click="getInfo">Go
						<view class="t"></view>
					</view>
				</view>
			</view>

			<!-- 电影列表 -->
			<view class="movieH">搜索结果</view>
			<view class="movieBox">
				<view v-for="(item, index) in movieInfo" :key="index" class="movieList" @click="getDate(item)">
					<image :src="item.img" class="movieImg"></image>
					<view>
						{{ item.title | ellipsis }}
						<view class="moiveRate">
							<!-- <uni-rate disabled="true" :value="item.rating.average/2" size=14 active-color="#D81E06" color="#DADADA">
							</uni-rate> -->
							<text class="moiveRateT"></text>
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
				path: 'pages/search/index' ,
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
		mounted() {
	
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
				inputInfo: '', // input数据
			}
		},
		methods: {
			messagesearch(e) {
				console.log(e.target.value)
				this.inputInfo = e.target.value
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
					url: 'https://api.douban.com/v2/movie/top250',
					method: 'GET',
					header: {
						"Content-Type": "application/text",
						"Cookie": 'bid=gmauOdTjPuM; ll="108296"; __utma=30149280.183190915.1588736614.1588736614.1588736614.1; __utmc=30149280; __utmz=30149280.1588736614.1.1.utmcsr=google|utmccn=(organic)|utmcmd=organic|utmctr=(not%20provided); __utma=223695111.1019919151.1588736616.1588736616.1588736616.1; __utmc=223695111; __utmz=223695111.1588736616.1.1.utmcsr=douban.com|utmccn=(referral)|utmcmd=referral|utmcct=/; _pk_ref.100001.4cf6=%5B%22%22%2C%22%22%2C1588736616%2C%22https%3A%2F%2Fwww.douban.com%2F%22%5D; _vwo_uuid_v2=DF41C6A193D708C4010E7A2D6F958507C|41887b5e2aa3fee81b7580e887d7e604; __yadk_uid=CaK93hfK60gv0yih8eYhMiiMaicvymVR; _pk_id.100001.4cf6=eb02d84691b41619.1588736616.1.1588736620.1588736616.; __gads=ID=5f256365a15a3bd0:T=1588736620:S=ALNI_MaoShIvp1GQXf-3uNudDn-YkC1ylA; dbcl2="199894991:L6mq4GWyH2I"; ck=C3EE'

					},
					data: {
						// type: 'movie',
						// tag: '喜剧',
						sort: 'recommend',
						count: 6,
						start: that.pageNum * 6,
						playable: 'on',
						apikey: '0df993c66c0c636e29ecbb5344252a4a'
					},
					success: function(res) {
						that.pageNum++;
						console.log(res);
						that.movieInfo = that.movieInfo.concat(res.data.subjects);
						that.listStatus = 'loading'
					}
				})
			},
			getInfo() {
				console.log(this.inputInfo)
				setTimeout(() => {
					this.$refs.loading.show() // 打开
				}, 10)
				uni.request({
					url: 'https://movie.douban.com/j/subject_suggest?q=' + this.inputInfo + '&page=30',
					header: {
						"Content-Type": "application/text",
					},
					success: (res) => {
						this.$refs.loading.hide() // 关闭
						console.log(res.data);
						this.movieInfo = res.data
						console.log(this.movieInfo, 'this.movieInfo ')
					}
				})
			},
			getDate(item) {
				uni.setStorage({
					key: 'storage_bg',
					data: item.img,
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
	.cu-custom {
		background-color: red
	}
	/* 搜索框 */
	.searchBox {
		position: absolute;
		left: 50%;
		margin-left: -35%;
		bottom: 140rpx;
		width: 70%;
		height: 80rpx;
		line-height: 80rpx;
		text-align: center;
		color: #3499cd;
		background-color: #FFFFFF;
		border-radius: 10rpx;
		overflow: hidden;
		display: flex;
		font-size: 32rpx;
	}

	.button {
		width: 20%;
		position: relative;
		color: #FFFFFF;
		background-color: #6C7CFD;
	}

	.search-t {
		height: 80rpx;
		padding: 0 40rpx 0 20rpx;
		flex: 1;
		text-align: left;
	}

	.t {
		border-width: 16rpx;
		border-style: solid;
		border-color: transparent #6C7CFD transparent transparent;
		position: absolute;
		right: 100%;
		top: 26rpx;
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
		height: 20rpx;
		line-height: 20rpx;
		display: flex;
		justify-content: space-between;
		align-items: center;
		margin-bottom: 15rpx;
	}

	.moiveRateT {
		margin-right: 4rpx;
	}
</style>
