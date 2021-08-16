<template>
	<view class="shop">
		<!-- 升级 -->
		<view>
			<yomol-upgrade :type="upgradeType" :url="upgradeUrl" title="发现新版本" :content="upgradeContent"
				ref="yomolUpgrade"></yomol-upgrade>
		</view>
		<view class="top">
			<u-toast ref="uToast" />
			<u-input class="shop-search" placeholder="请输入商品名称" placeholder-style="color: #9b9b9b" input-align="center"
				height="60" border="true" border-color="#eeeeee" v-model="searchVal" type="text" @confirm='onSearch' />
			<!-- <image @click="onSearch" class="search-icon" src="/static/search.png" mode=""></image> -->
		</view>
		<view class="banner">
			<u-swiper mode='number' indicator-pos="bottomRight" height='801' :list="swiperList"></u-swiper>
		</view>
		<view class="banner-bottom">
			<view class="nav1">
				<image class="pic" src="/static/zu685.png" mode=""></image>
				<view class="txt">质量保障</view>
			</view>
			<view class="nav2">
				<image class="pic" src="/static/lujin880.png" mode=""></image>
				<view class="txt">品质舒适生活</view>
			</view>
			<view class="dian"></view>
			<view class="nav3">
				<image class="pic" src="/static/lujin881.png" mode=""></image>
				<view class="txt">自营正品保证</view>
			</view>
			<view class="dian"></view>
			<view class="nav4">
				<image class="pic" src="/static/zu686.png" mode=""></image>
				<view class="txt">包邮送到家</view>
			</view>
		</view>
		<view class="items">
			<view class="item" @tap="toNewGood">
				<view class="item-cir">
					<view class="circle">
						<image class="img1" src="/static/zu687.png" mode=""></image>
					</view>
				</view>
				<view class="circle-title">
					好物上新
				</view>
			</view>
			<view class="item" @tap="toMiandanjilu">
				<view class="item-cir">
					<view class="circle">
						<image class="img2" src="/static/zu688.png" mode=""></image>
					</view>
				</view>
				<view class="circle-title">
					免单记录
				</view>
			</view>
			<view class="item" @tap="toYaoqinhaoyou">
				<view class="item-cir">
					<view class="circle">
						<image class="img3" src="/static/zu714.png" mode=""></image>
					</view>
				</view>
				<view class="circle-title">
					邀请好友
				</view>
			</view>
		</view>
		<view class="hots-arr">
			<view class="hot-list" @tap="toZhuanqu(item)" v-for="item in bannerlist" :key="item.title">
				<view class="hots">
					<image class="hotImg" :src="item.img" mode=""></image>
					<view class="bigT">{{item.title}}</view>
					<view class="smallT">{{item.sub_title}}</view>
				</view>
			</view>
		</view>
		<view class="henggang"></view>
		<view class="container">
			<view class="nav1">
				<view class="tit1">
					<view class="tit1-1"></view>
					<view class="tit1-2">大牌推荐</view>
				</view>
				<!-- <view class="tit2">
					<view class="tit2-1">更多</view>
					<u-icon name="arrow-right" color="#9b9b9b" size="20"></u-icon>
				</view> -->
			</view>
			<view class="nav2">
				<!-- 横向 -->
				<!-- 				<scroll-view class="scroll-view" scroll-x @scroll="scroll" style="width: 100%;white-space:nowrap;">
					<image @click="toXiangqin(item.id)" v-for="(item,i) in list" class="pic1" :src="item.img" mode="">
					</image>
				</scroll-view> -->
				<!-- 纵向 -->
				<scroll-view class="scroll-view y" scroll-y @scroll="scroll">
					<view class="box" v-for="(item,i) in list" :key='i'>
						<image @click="toXiangqin(item.id)" class="pic1" :src="item.img" mode="">
						</image>
						<view class="title">{{item.title}}</view>
						<view class="nav1">￥{{item.price}}</view>
					</view>

				</scroll-view>
			</view>
		</view>

		<u-tabbar height=98 active-color="#EBBFCC" mid-button-size=39 v-model="current" :list="tabbarlist"
			:mid-button="true"></u-tabbar>
	</view>
</template>

<script>
	import yomolUpgrade from '@/components/yomol-upgrade/yomol-upgrade.vue'
	export default {
		components: {
			yomolUpgrade
		},
		data() {
			return {
				// 升级
				upgradeType: 'pkg', //pkg 整包 wgt 升级包
				upgradeContent: '123', //更新内容
				upgradeUrl: '', //更新地址
				// 
				searchVal: '',
				bannerList: [
					'/static/hongbaobanner.png',
					'/static/img@2x.png'
				],
				swiperList: [],
				bannerlist: [],
				list: [],
				tabbarlist: [{
						iconPath: "/static/shouye.png",
						selectedIconPath: "/static/shouye-active.png",
						text: '首页',
						midButton: true,
						pagePath: "/pages/index/index"
					},
					{
						iconPath: "/static/miandan.png",
						selectedIconPath: "/static/miandan-active.png",
						text: '免单',
						customIcon: false,
						pagePath: "/pages/freeCharge/freeCharge"
					},
					{
						iconPath: "/static/zu27.png",
						selectedIconPath: "static/zu242.png",
						text: '购物车',
						customIcon: false,
						pagePath: "/pages/gouwuche/gouwuche"
					},
					{
						iconPath: "/static/zu243.png",
						selectedIconPath: "/static/zu245.png",
						text: '我的',
						customIcon: false,
						pagePath: "/pages/wode/wode"
					},
				],
				current: 0
			}
		},
		//用户点击右上角分享转发
		onShareAppMessage: async function() {
			const res = await this.$api.wx_sharetouserid(this.openid);
			console.log(res)

			var title = '拼60商城app'; //data，return 数据title
			return {
				title: title || '',
				path: `/pages/index/index?scene=0_${res.share_userid}`,
			}
		},
		//用户点击右上角分享朋友圈
		onShareTimeline:async function() {
			const res = await this.$api.wx_sharetouserid(this.openid);
			console.log(res)
			var title = '拼60商城app'; //data，return 数据title
			return {
				title: title || '',
				path: `/pages/index/index?scene=0_${res.share_userid}`,
			}
		},
		async onLoad(option) {
			// 升级
			var upid;
			if (uni.getSystemInfoSync().platform == 'ios') {
				upid = 2
				const res = await this.$api.app_upgrade({
					upid,
					version: plus.runtime.version
				});
				console.log(res)
				if (res.result == 1) {
					if (res.is_upgrade == 0) {
						return
					} else if (res.is_upgrade == 1) {
						this.upgradeType = 'pkg'
						this.upgradeContent = res.upgrade_contents;
						this.upgradeUrl = res.down_url
						this.$refs.yomolUpgrade.show()
					}
				};
			} else if (uni.getSystemInfoSync().platform === 'android') {
				upid = 1
				const res = await this.$api.app_upgrade({
					upid,
					version: plus.runtime.version
				});
				console.log(res)
				if (res.result == 1) {
					if (res.is_upgrade == 0) {
						return
					} else if (res.is_upgrade == 1) {
						this.upgradeType = 'pkg'
						this.upgradeContent = res.upgrade_contents;
						this.upgradeUrl = res.down_url
						this.$refs.yomolUpgrade.show()
					}
				};
			} else {
				return;
			}
			// 
			this.openid = uni.getStorageSync('openid')
			console.log(option)
			if (option.scene) {
				const arr = option.scene.split('_')
				uni.setStorageSync('scene', arr[1])
				uni.setStorageSync('myUserId', arr[1])
				if (this.openid) {
					var signstr = "openid=" + this.openid + "&recommend_userid=" + arr[1] + "";
					const md51 = this.$md5(signstr);
					const md52 = md51 + this.$apikey;
					const md = this.$md5(md52).toUpperCase()
					const resp = await this.$api.wx_userrecommend(
						this.openid,
						arr[1],
						md
					)
					console.log(resp)
				} else {
					uni.navigateTo({
						url: `/weixinshouquan/pages/weixinshouquan?recommend_userid=${arr[1]}`
					})
				}
			} else {
				uni.setStorageSync('scene', 0)
			}
			console.log(uni.getStorageSync('scene'))
			this.getData()
		},
		created() {
			this.getData()
		},
		methods: {
			async getData() {
				// console.log(plus.runtime.version)
				const res = await this.$api.wx_index()
				console.log(res)
				this.swiperList = [res.swiperList.first_pic, res.swiperList.second_pic, res.swiperList.third_pic]
				this.bannerlist = res.bannerlist;
				this.list = res.list;
			},
			onSearch() {
				if (this.searchVal != '') {
					uni.navigateTo({
						url: `/pages/nanzhuanzhuanqu/nanzhuanzhuanqu?keyword=${this.searchVal}`
					})
				} else {
					this.$refs.uToast.show({
						title: '请输入关键词再搜索',
						type: 'warning',
						duration: 1000,
					})
				}

			},
			scroll: function(e) {
				// console.log(e)
				// this.old.scrollTop = e.detail.scrollTop
			},
			toNewGood() {
				console.log(123)
				uni.navigateTo({
					url: '/pages/index/newGoodThings'
				})
			},
			toZhuanqu(item) {
				console.log(item)
				if (item.id == 1) {
					uni.navigateTo({
						url: `/pages/nanzhuanzhuanqu/nanzhuanzhuanqu?is_hot=1&navTitle=热卖榜单`
					})
				} else if (item.id == 2) {
					uni.navigateTo({
						url: '/pages/nanzhuanzhuanqu/nanzhuanzhuanqu?is_new=1&navTitle=新品上新'
					})
				} else if (item.id == 3) {
					uni.navigateTo({
						url: '/pages/nanzhuanzhuanqu/nanzhuanzhuanqu?is_brand=1&navTitle=国际大牌'
					})
				} else if (item.id == 4) {
					uni.navigateTo({
						url: '/pages/nanzhuanzhuanqu/nanzhuanzhuanqu?is_zone=1&navTitle=男装专区'
					})
				}
				// uni.navigateTo({
				// 	url: '/pages/nanzhuanzhuanqu/nanzhuanzhuanqu'
				// })
			},
			toMiandanjilu() {
				uni.navigateTo({
					url: '/pages/miandanjilu/miandanjilu'
				})
			},
			toYaoqinhaoyou() {
				uni.navigateTo({
					// url:'/pages/yaoqinghaoyou/yaoqinghaoyou'
					url: '/yaoqinghaoyou/pages/yaoqinghaoyou'
				})
			},
			toXiangqin(id) {
				uni.navigateTo({
					url: `/pages/shangpinxiangqin/shangpinxiangqin?id=${id}`
				})
			}
		}
	}
</script>

<style scoped lang="scss">
	.top {
		display: flex;
		align-items: center;
		justify-content: center;
		margin: 30rpx 49rpx 0 49rpx;

		.shop-search {
			width: 658rpx;
			height: 60rpx;
			opacity: 1;
			border-radius: 30rpx;
		}

		.search-icon {
			width: 40rpx;
			height: 40rpx;
			opacity: 1;
			margin-left: 28rpx;
		}
	}

	.banner {
		box-shadow: 0rpx 5rpx 7rpx 0rpx rgba(0, 0, 0, 0.05);
		margin: 0 auto;
		margin-top: 26rpx;
		width: 750rpx;
		height: 801rpx;
		opacity: 1;
		background: rgba(0, 0, 0, 0.00);

		image {
			height: 100%;
			width: 100%;
		}
	}

	.banner-bottom {
		width: 750rpx;
		height: 58rpx;
		opacity: 1;
		background: #f8f8f8;
		display: flex;
		align-items: center;

		.nav1 {
			display: flex;
			align-items: center;
			width: 141rpx;
			height: 40rpx;
			opacity: 1;
			background: #ebbfcc;
			border-radius: 0rpx 20rpx 20rpx 0rpx;

			.pic {
				margin-left: 17rpx;
				margin-right: 11rpx;
				width: 20rpx;
				height: 18rpx;
				opacity: 1;
			}

			.txt {
				opacity: 1;
				font-size: 18rpx;
				font-family: PingFang SC, PingFang SC-Bold;
				font-weight: 700;
				text-align: center;
				color: #ffffff;
			}
		}

		.nav2 {
			margin-left: 36rpx;
			display: flex;
			align-items: center;
			height: 40rpx;
			opacity: 1;

			.pic {
				margin-right: 7rpx;
				width: 20rpx;
				height: 18rpx;
				opacity: 1;
			}

			.txt {
				opacity: 1;
				font-size: 18rpx;
				font-family: PingFang SC, PingFang SC-Bold;
				font-weight: 700;
				text-align: center;
				color: #484848;
			}
		}

		.dian {
			width: 7rpx;
			height: 7rpx;
			opacity: 1;
			background: #ebbfcc;
			border-radius: 50%;
			margin: 0 18rpx 0 20rpx;
		}

		.nav3 {
			display: flex;
			align-items: center;
			height: 40rpx;
			opacity: 1;

			.pic {
				margin-right: 7rpx;
				width: 20rpx;
				height: 18rpx;
				opacity: 1;
			}

			.txt {
				opacity: 1;
				font-size: 18rpx;
				font-family: PingFang SC, PingFang SC-Bold;
				font-weight: 700;
				text-align: center;
				color: #484848;
			}
		}

		.nav4 {
			display: flex;
			align-items: center;
			height: 40rpx;
			opacity: 1;

			.pic {
				margin-right: 7rpx;
				width: 20rpx;
				height: 18rpx;
				opacity: 1;
			}

			.txt {
				opacity: 1;
				font-size: 18rpx;
				font-family: PingFang SC, PingFang SC-Bold;
				font-weight: 700;
				text-align: center;
				color: #484848;
			}
		}
	}

	.items {
		display: flex;
		justify-content: space-between;
		margin: 54rpx 100rpx 0rpx 100rpx;

		.item {
			opacity: 1;

			.item-cir {
				width: 110rpx;
				height: 110rpx;
				border: 2rpx solid #ebbfcc;
				border-radius: 60rpx;
			}

			.circle {
				margin: 7rpx 6rpx 6rpx 7rpx;
				display: flex;
				justify-content: center;
				align-items: center;
				width: 94rpx;
				height: 94rpx;
				opacity: 1;
				border-radius: 51rpx;
				background-color: #ebbfcc;

				.img1 {
					width: 53rpx;
					height: 49rpx;
					opacity: 1;
				}

				.img2 {
					width: 42rpx;
					height: 49rpx;
					opacity: 1;
				}

				.img3 {
					width: 56rpx;
					height: 47rpx;
					opacity: 1;
				}
			}

			.circle-title {
				margin-top: 16rpx;
				opacity: 1;
				font-size: 20rpx;
				font-family: SourceHanSansCN-Regular;
				text-align: center;
				color: #333333;
				line-height: 30rpx;
			}
		}
	}

	.hots-arr {
		// margin: 0 22rpx 0 22rpx;
		margin: 0 49rpx;
		display: flex;
		flex-wrap: wrap;
		justify-content: space-between;

		.hot-list {
			overflow: hidden;
			margin-top: 30rpx;
			width: 321rpx;
			height: 214rpx;
			opacity: 1;
			// background: url(/static/img.png) no-repeat;
			background-size: 100% 100%;
			position: relative;

			.hots {
				margin-top: 36rpx;

				.hotImg {
					position: absolute;
					top: 0;
					left: 0;
					width: 354rpx;
					height: 236rpx;
				}

				.bigT {
					position: absolute;
					top: 33rpx;
					left: 33rpx;
					width: 128rpx;
					heiFght: 32rpx;
					opacity: 1;
					font-size: 32rpx;
					color: #4a4a4a;
					line-height: 48rpx;
					font-weight: bold;
				}

				.smallT {
					position: absolute;
					top: 80rpx;
					left: 33rpx;
					width: 144rpx;
					height: 24rpx;
					opacity: 1;
					font-size: 24rpx;
					text-align: left;
					color: #9b9b9b;
					line-height: 36rpx;
				}
			}
		}
	}

	.henggang {
		margin: 63rpx auto 37rpx auto;
		width: 257rpx;
		height: 4rpx;
		opacity: 1;
		background: #121212;
	}

	.container {
		.nav1 {
			margin: 0rpx 54rpx 21rpx 52rpx;
			display: flex;
			justify-content: space-between;

			.tit1 {
				display: flex;
				align-items: center;

				.tit1-1 {
					width: 4rpx;
					height: 22rpx;
					opacity: 1;
					background: #121212;
				}

				.tit1-2 {
					margin-left: 8rpx;
					opacity: 1;
					font-size: 24rpx;
					font-family: PingFang SC, PingFang SC-Regular;
					font-weight: 400;
					text-align: left;
					color: #121314;
				}
			}

			.tit2 {
				display: flex;
				align-items: center;

				.tit2-1 {
					opacity: 1;
					font-size: 18rpx;
					font-family: PingFang SC, PingFang SC-Regular;
					font-weight: 400;
					color: #9b9b9b;
					margin-right: 12rpx;
				}
			}
		}

		.nav2 {
			margin: 14rpx 40rpx;

			.box {
				display: inline-block;
				width: 315rpx;
				margin-right: 14rpx;

				.pic1 {
					width: 315rpx;
					height: 312rpx;
					opacity: 1;
					margin-left: 13rpx;
					margin-bottom: 16rpx;
				}

				.title {
					margin-left: 13rpx;
					padding: 0;
					width: 100%;
					opacity: 1;
					font-size: 22rpx;
					font-family: PingFang SC, PingFang SC-Regular;
					font-weight: 400;
					color: #121314;
					line-height: 27rpx;
					letter-spacing: 1rpx;
					overflow: hidden;
					text-overflow: ellipsis;
					white-space: nowrap;
				}

				.nav1 {
					padding: 0;
					margin: 0;
					margin-left: 13rpx;
					width: 100%;
					opacity: 1;
					font-size: 31rpx;
					font-family: PingFang SC, PingFang SC-Bold;
					font-weight: 700;
					color: #af0000;
					line-height: 92rpx;
				}
			}

			.scroll-view.y {
				display: flex;
				justify-content: space-around;
			}

		}

	}

	/deep/ .u-tabbar__content__circle__border {
		display: none !important;
	}
</style>
