<template>
	<view class="detailBox">
		<!-- loading动画 -->
		<mi-loading ref="loading" title="加载中" :hasMask="true"></mi-loading>
		<view class="detailBgMax">
			<view class="detailBg" :style="{backgroundImage: 'url(' + viewBg + ')'}">></view>
		</view>
		<cu-custom :isBack="true">
			<block slot="backText"> </block>
			<block slot="content" class="tarbar">{{ viewTitle }}</block>
		</cu-custom>
		<scroll-view class="scrollBox">
			<view :class="summarStatu ? 'textMark' : ''">
				<view class="bigImgBox">
					<image :src="viewBanner.small" class="bigImg"></image>
				</view>
				<view class="bigtitleBox">
					<text v-if="summarStatu">{{ viewTitle }}</text>
					<br>
					<text class="textSty">{{ countries }}{{ genres }}{{ pubdates }}{{ durations }}</text>
				</view>
				<view class="summarybox">
					<text v-if="summarStatu">剧情简介: </text>
					<br>
					<view class="summary">
						<template v-if="showText">
							{{summary}}
							<text v-if="summary !== null && summary.length > 85" class="full_text" @click="toggleDescription"></text>
						</template>
						<template v-else>
							<text v-if="summarStatu">{{summary.substr(0, 85) + '... ' }}</text>
							<text v-if="summary !== null && summary.length > 85" class="full_text" @click="toggleDescription">全文</text>
						</template>

					</view>
				</view>
				<view class="summarybox">
					<text v-if="summarStatu">演职员: </text>
				</view>

				<view class="castsBox">
					<view v-for="(item, index) in directors" :key="item.id" class="castsBox-li">
						<view>
							<img :src="item.avatars.small" alt="导演">
							<text class="summaryColor">{{ item.name }}</text>
							<br>
							<text class="summaryVColor">导演</text>
						</view>
					</view>
					<view v-for="(item, index) in casts" :key="index" class="castsBox-li">
						<view>
							<img :src="item.avatars.small" alt="演员">
							<text class="summaryColor">{{ item.name }}</text>
							<br>
							<text class="summaryVColor">演员</text>
						</view>
					</view>
				</view>
				<!--        评论-->
				<view class="popular_comments" style="margin-top: 50rpx;">
					<text class="hTitle" v-if="summarStatu">精选评论</text>
					<view class="popular_comments-page" v-for="(item, index) in popular_comments" :key="index" style="width: 94%;margin: 14rpx auto;border-bottom: 1px solid rgba(204, 204, 204, .1);">
						<view class="commentsPage-t" style="display: flex; height: 100rpx;align-items: center">
							<img :src="item.author.avatar" alt="" style="width: 80rpx;height: 80rpx;border-radius: 40rpx;">
							<view style="display: flex;flex-direction: column;justify-content: space-around;height: 100rpx;">
								<text style="font-size: 30rpx;height: 30rpx;color: #fff;font-weight: 700;margin-left: 20rpx;">{{ item.author.name }}</text>
								<view style="margin-left: 20rpx;height: 20rpx;">
									<uni-rate disabled="true" :value="item.rating.value/2" size=14 active-color="#ffd21e" color="#DADADA">
									</uni-rate>
								</view>
							</view>
						</view>
						<view style="font-size: 30rpx;padding: 10rpx 0 10rpx 0;color: #fff;">
							{{ item.content }}
						</view>
						<view style="font-size: 30rpx;color: #b7acac;text-align: right;margin-bottom: 8rpx">{{ item.created_at }}</view>
					</view>
				</view>
			</view>
		</scroll-view>
		<!--        分割线-->
		<!-- #ifdef MP-WEIXIN || MP-BAIDU -->
		<view style="width: 98%;margin: 0 auto;" v-if="trailersState">
				<view  v-if="summarStatu" style="height: 100rpx;display: flex;align-items: center;color: #007AFF;z-index: 2;font-size: 40rpx;color: #EDE1D9;font-weight: 700;argin-left: 3%;;position: relative;">
					预告片 / 剧照
					<text></text>
				</view>
			<!-- <img src="http://img.zcool.cn/community/01e858594b2e58a8012193a3da10db.gif" alt="" style="width: 100%; height: 130rpx"> -->
			<view class="functionaryJob" style="height: 340rpx;display: flex;overflow: auto;">
				<view class="directors" style="display: flex;width: auto;	" v-for="item of trailers" :key="item.id">
					<view style="width:340rpx;height: 192rpx;position: relative; margin-right: 18rpx;">
						<video controls :poster='item.medium' :src="item.resource_url" style="width:340rpx;height: 192rpx;border-radius: 14rpx 14rpx 0 0;">
						</video>

						<view style="display: flex;align-items: center;justify-content: center;width: 340rpx;height: 80rpx;line-height: 40rpx;background-color: #222;color: #fff;position: absolute;bottom: -80rpx;font-size: 30rpx;border-radius: 0 0 14rpx 14rpx;text-align: center;">
							<text>{{ item.title }}</text>
						</view>
						</view>
					</view>
					<view class="directors" style="display: flex;width: auto;	" v-for="item of photos" :key="item.id">
						<view style="width:250rpx;height: 192rpx;position: relative; margin-right: 20rpx;">
							<image  :src="item.cover" style="width:250rpx;height: 272rpx;border-radius: 10rpx">
							</image>
					
							<!-- <view style="display: flex;align-items: center;justify-content: center;width: 340rpx;height: 80rpx;line-height: 40rpx;background-color: #222;color: #fff;position: absolute;bottom: -80rpx;font-size: 30rpx;border-radius: 0 0 14rpx 14rpx;text-align: center;">
								<text>剧照</text>
							</view> -->
							</view>
						</view>
					</view>
				</view>

				

			</view>

		</view>
		<!-- #endif -->
	</view>
</template>

<script>
	import miLoading from '../../components/mi-loading/mi-loading.vue' // loading动画
	export default {
		components: {
			miLoading
		},
		onShareAppMessage: function(res) {
			var that = this;
			return {
				title: '老赵的杂货铺',
				path: 'pages/detail/index' ,
				success: function(res) {
					console.log("转发成功:" + JSON.stringify(res));
					that.shareClick();
				},
				fail: function(res) {
					console.log("转发失败:" + JSON.stringify(res));
				}
			}
		},
		onLoad: function(option) {
			setTimeout(() => {
				that.$refs.loading.show() // 打开
			}, 10)
			// uni.setBackgroundTextStyle({
			//   textStyle: 'light' // 下拉背景字体、loading 图的样式为dark
			// })
			// uni.showLoading({
			// 	'title': '加载中'
			// })
			var that = this
			// 获取本地存储的图片
			uni.getStorage({
				key: 'storage_bg',
				success: function(res) {
					that.viewBg = res.data
				}
			});
			uni.getStorage({
				key: 'storage_h1',
				success: function(res) {
					that.viewTitle = res.data
				}
			});
			// 获取电影详情信息
			var id = option.id
			uni.request({
				url: 'https://api.douban.com/v2/movie/subject/' + id,
				method: 'GET',
				header: {
					"Content-Type": "application/text",
				},
				data: {
					apikey: '0df993c66c0c636e29ecbb5344252a4a'
				},
				success: function(res) {
					setTimeout(() => {
						that.$refs.loading.hide() // 关闭
					}, 10)
					that.info = res.data
					console.log(res.data)
					that.viewBanner = res.data.images //电影封面
					that.genres = res.data.genres[0] + ' / ' //电影类型
					that.countries = res.data.countries[0] + ' / ' // 上映国家
					that.pubdates = (res.data.pubdates[0] ? res.data.pubdates[0] : res.data.pubdates) + ' / ' // 上映时间
					that.durations = '片长: ' + res.data.durations[0] ? res.data.durations[0] : res.data.durations // 片长
					that.summary = res.data.summary // 剧情简介
					that.directors = res.data.directors // 导演
					that.casts = res.data.casts // 演员
					// #ifdef MP-WEIXIN || MP-BAIDU 
					that.photos = res.data.photos // 剧照
					that.trailers = res.data.trailers // 预告片
					if(that.trailers.length < 1) {
						that.trailersState = false
					}
					// #endif
					that.popular_comments = res.data.popular_comments // 评论

					that.summarStatu = true
					if (res.data.videos[0]) {
						that.isVideosLook = true
						that.movieUrl = res.data.videos[0].sample_link
					} else {
						that.isVideosLook = false
					}
				}
			})
		},
		data() {
			return {
				info: {}, // 电影信息
				genres: '', // 电影类型
				countries: '', // 上映国家
				pubdates: '', // 上映时间
				durations: '', // 片长
				viewBg: '', // 背景img
				viewTitle: '', // 电影title
				viewBanner: {}, //电影封面
				summary: '', // 剧情简介
				casts: [], // 演员
				directors: [], // 导演
				summarStatu: false, // 剧情简介是否显示
				movieUrl: '', // 电影视频
				trailers: [], // 预告片
				photos: [], // 剧照
				trailersState: true, //预告片状态
				popular_comments: [], // 精选评论
				isVideosLook: false, // 是否可以观看
				showText: false, // 展开收起
			}
		},
		methods: {
			toggleDescription(num) {
				this.showText = !this.showText
			}
		},
	}
</script>

<style scoped>
	/* 文字遮罩 */
	.textMark {
		margin-top: 20rpx;
		border-radius: 80rpx 80rpx 14rpx 14rpx;
		background-color: rgba(0, 0, 0, .2);
	}

	.detailBox {
		width: 100%;
		height: 100vh;
		position: relative;
	}

	.detailBgMax {
		position: fixed;
		overflow: hidden;
		width: 100%;
		height: 100vh;
		top: 0;
		left: 0;
		z-index: 0;
	}

	.detailBg {
		width: 100%;
		height: 100vh;
		background-size: 100% 100%;
		filter: blur(60rpx);
		transform: scale(1.2);
		transition: 0.3s;
	}

	/* 导航tab */
	.tarbar {
		font-weight: 700 !important;
		color: #fff !important;
	}

	/* 大图 */
	.bigImgBox {
		height: 700rpx;
		/* background: pink; */
		transition: 1s;
		text-align: center;
	}

	/* 滚动视图窗口 */
	.scrollBox {}

	/* 图片从上至下动画 */
	@keyframes mymove {
		0% {
			margin-top: -200px
		}

		100% {
			margin-top: 50px
		}
	}

	.bigImg {
		width: 440rpx;
		height: 600rpx;
		margin-top: 50px;
		border-radius: 10rpx;
		animation: mymove 0.8s cubic-bezier(.41, -0.6, .68, .81) 1;
		/* -moz-box-shadow: 1px 1px 1px #dddddd;
		-webkit-box-shadow: 1px 1px 1px #dddddd;
		box-shadow: 1px 1px 1px #dddddd; */
	}

	.bigtitleBox {
		width: 90%;
		margin: 0 auto;
		text-align: center;
		line-height: 80rpx;
		font-weight: 700;
		color: #F7F7F7;
	}

	.textSty {
		width: 90%;
		margin: 0 auto;
	}

	/* 剧情简介 */
	.summarybox {
		width: 90%;
		margin: 40rpx auto 0;
		line-height: 60rpx;
		font-size: 40rpx;
		color: #EDE1D9;
		font-weight: 700;
	}

	/* 标题设置 */
	.hTitle {
		font-size: 40rpx;
		color: #EDE1D9;
		font-weight: 700;
		margin-left: 3%;
	}
	/* 演职员设置 */
	.castsBox {
		width: 90%;
		display: flex;
		margin: 30rpx auto 0;
		overflow: auto;
	}

	.castsBox-li {
		margin-right: 30rpx;
		text-align: center;
		;
	}

	.castsBox-li img {
		width: 200rpx;
		height: 260rpx;
		border-radius: 10rpx;
	}

	.summary {
		color: #fff;
		font-weight: 600;
		font-size: 30rpx;
		line-height: 40rpx;
		margin-top: 20rpx;
		/* max-height: 160upx; */
	}

	.summaryColor {
		color: #D3BBA7;
	}

	.summaryVColor {
		color: #fff;
		font-weight: 700;
	}

	/* 视频设置 */
	@-webkit-keyframes masked-animation {
		0% {
			background-position: 0 0
		}

		to {
			background-position: -100% 0
		}
	}

	.videoUrl {
		margin-top: 30rpx;
	}

	.play {
		color: #FFFFFF;
		font-size: 50rpx;
		font-weight: 700;
		line-height: 140rpx;
		text-align: center;
		background-image: -webkit-linear-gradient(left, #02ab6c, #DD524D 25%, #3499cd 50%, #DD524D 75%, #3499cd);
		-webkit-text-fill-color: transparent;
		-webkit-background-clip: text;
		-webkit-background-size: 200% 100%;
		-webkit-animation: masked-animation 1s infinite linear;
	}
</style>
