<template>
	<view class="index">
		<u-toast ref="uToast" />
		<image class="picc1111" src="/static/zu729.png" mode=""></image>
		<view class="container">
			<view class="nav1">
				<image class="pic1" src="/static/zu1411.png" mode=""></image>
				<!-- <view class="tit1"> -->
				<view class="txt1">我累计获得的奖励</view>
				<view class="txt2">
					<view class="tit11">￥</view>
					<view class="tit22">{{commission_money}}</view>
					<!-- </view> -->
				</view>
				<view class="tit2" @tap="toLijitixian">提现</view>
			</view>
			<u-notice-bar class="nav2" font-size='22' :volume-icon="false" mode="vertical" :list="gdList">
			</u-notice-bar>
			<view class="nav3" @tap="wodetuanduiShow=true">我的团队</view>
			<view class="nav4" @tap="toYaoqinerweima">邀请好友</view>

		</view>
		<!-- 链接邀请 -->
		<u-popup class="wodetuandui" height="800rpx" v-model="wodetuanduiShow" mode="center" border-radius="14"
			width="681rpx">
			<view class="footer">
				<view class="box">
					<view class="items">
						<view class="item" v-for="item in list" :key="item">
							<image class="pic" :src="item.facepic" mode=""></image>
							<view class="tit">
								<view class="tit1">{{item.nickname}}</view>
								<view class="tit2">{{item.remark}}</view>
							</view>
						</view>
					</view>
				</view>
			</view>
		</u-popup>
		<!-- 链接邀请 -->
		<u-popup class="ljyq" v-model="showLjyq" mode="center" border-radius="14" width="681rpx" height="364rpx">
			<view class="tit1">邀请链接</view>
			<view class="tit2">
				<view class="tit">{{yqlink}}</view>
			</view>
			<view class="tit3" @tap="copy">复制</view>
		</u-popup>
		<!-- 分享二维码 -->
		<u-popup v-model="fenxiangShow" mode="center" border-radius="14">
			<image class="fxImg" :src="fenxiangImgSrc" mode=""></image>
			<button @click="downImg" class="downImg">下载到本地</button>
		</u-popup>

	</view>
</template>

<script>
	import {
		mapState
	} from "vuex";
	export default {
		computed: {
			...mapState(["yqhyPage", "yqhyPageSize"]),
		},
		watch: {
			yqhyPage: function(yqhyPage) {
				this.$store.commit("yqhyPage", yqhyPage);
				this.getData();
			},
		},
		data() {
			return {
				gdList: [],
				isActive: true,
				showLjyq: false,
				wodetuanduiShow: false,
				yqlink: 'DSJFGASKDZ41512',
				openid: null,
				commission_money: 0,
				list: [],
				fenxiangShow: false,
				fenxiangImgSrc: '',
				imgDownSrc: '',
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
		async onShow() {
			this.openid = uni.getStorageSync('openid')
			this.getData()
		},
		methods: {
			async getData() {
				const res = await this.$api.wx_usercommission(this.openid, this.yqhyPage, this.yqhyPageSize);
				console.log(res)
				this.commission_money = res.commission_money;
				this.list = res.list;
				const res2 = await this.$api.wx_usertopcommission(this.openid)
				console.log(res2)
				res2.list.forEach(ele => {
					var time = this.formatMsgTime(new Date(ele.recommend_time).getTime())
					var gd = `${time}邀请${ele.nickname} 已获得${ele.money}元佣金`
					this.gdList.push(gd)
				})
			},
			formatMsgTime(timespan) {
				var dateTime = new Date(timespan);
				var year = dateTime.getFullYear();
				var month = dateTime.getMonth() + 1;
				var day = dateTime.getDate();
				var hour = dateTime.getHours();
				var minute = dateTime.getMinutes();
				var second = dateTime.getSeconds();
				var now = new Date();
				var now_new = Date.parse(now.toDateString()); //typescript转换写法
				var milliseconds = 0;
				var timeSpanStr;
				milliseconds = now_new - timespan;
				if (milliseconds <= 1000 * 60 * 1) {
					timeSpanStr = '刚刚';
				} else if (1000 * 60 * 1 < milliseconds && milliseconds <= 1000 * 60 * 60) {
					timeSpanStr = Math.round((milliseconds / (1000 * 60))) + '分钟前';
				} else if (1000 * 60 * 60 * 1 < milliseconds && milliseconds <= 1000 * 60 * 60 * 24) {
					timeSpanStr = Math.round(milliseconds / (1000 * 60 * 60)) + '小时前';
				} else if (1000 * 60 * 60 * 24 < milliseconds && milliseconds <= 1000 * 60 * 60 * 24 * 15) {
					timeSpanStr = Math.round(milliseconds / (1000 * 60 * 60 * 24)) + '天前';
				} else if (milliseconds > 1000 * 60 * 60 * 24 * 15 && year == now.getFullYear()) {
					timeSpanStr = month + '-' + day + ' ' + hour + ':' + minute;
				} else {
					timeSpanStr = year + '-' + month + '-' + day + ' ' + hour + ':' + minute;
				}
				return timeSpanStr;
			},
			getActive(val) {
				if (val == 'tab1') {
					this.isActive = false;
				} else {
					this.isActive = true;
				}
			},
			goTo() {
				uni.switchTab({
					url: "/pages/index/index"
				})
			},
			downImg() {
				const that = this;
				uni.downloadFile({
					url: that.imgDownSrc,
					success(res) {
						console.log(res)
						uni.saveImageToPhotosAlbum({
							filePath: res.tempFilePath,
							success() {
								console.log(res.tempFilePath, '成功')
								that.$refs.uToast.show({
									title: '已保存至相册',
									type: 'success',
								})
							},
							fail(res) {
								console.log('fail')
							},
						})
					}
				})
			},
			async toYaoqinerweima() {
				uni.showLoading({
					title: '加载中'
				});
				this.fenxiangShow = true;
				var signstr = "openid=" + this.openid + "&goods_id=" + 0 + "";
				const md51 = this.$md5(signstr);
				const md52 = md51 + this.$apikey;
				const md = this.$md5(md52).toUpperCase()
				const res = await this.$api.wx_shareqr(this.openid, 0, md)
				console.log(res, 'cyy111')
				this.fenxiangImgSrc = res.pic_url;
				this.imgDownSrc = res.pic_url;
				uni.hideLoading();
			},
			copy() {
				uni.setClipboardData({
					data: this.yqlink,
					success: function() {
						console.log('success');
					}
				})
			},
			changeYqlj() {
				this.showLjyq = true
			},
			toLijitixian() {
				uni.navigateTo({
					url: `/pages/wode/user/lijitixian?withdrawal_money=${this.commission_money}`
				})
			},
		}
	}
</script>

<style scoped lang="scss">
	/* 	page{
		width: 750rpx;
		height: 1333rpx;
	} */
	.index {
		position: relative;
		height: 100vh;
		width: 100%;
		// background: rgb(255, 46, 70);
		background: #FFFFFF;
	}

	.picc1111 {
		position: absolute;
		top: 0;
		left: 0;
		width: 750rpx;
		height: 806rpx;
		opacity: 1;
	}

	.nav {
		display: flex;
		align-items: center;
		height: 36rpx;
		// padding: 60rpx 50rpx 33rpx 46rpx;
		margin: 0rpx 50rpx 0rpx 46rpx;

		.nav-title {
			margin-left: 23rpx;
			opacity: 1;
			font-size: 36rpx;
			font-family: SourceHanSansCN-Regular;
			text-align: left;
			color: #121212;
			line-height: 71rpx;
		}
	}

	.container {
		position: relative;
		width: 750rpx;
		height: 100%;

		// margin: 33rpx 43rpx 0 43rpx;

		.nav1 {
			z-index: 3;
			position: absolute;
			top: 546rpx;
			left: 50%;
			transform: translateX(-50%);
			width: 717rpx;
			height: 322rpx;
			opacity: 0.9;

			.pic1 {
				position: absolute;
				top: 0;
				left: 0;
				width: 717rpx;
				height: 322rpx;
				opacity: 1;
			}

			// .tit1{
			// 	position: absolute;
			// 	top: 85rpx;
			// 	display: flex;
			// 	align-items: center;
			.txt1 {
				position: absolute;
				top: 103rpx;
				margin-left: 72rpx;
				margin-right: 36rpx;
				opacity: 1;
				font-size: 22rpx;
				font-family: PingFang SC, PingFang SC-Regular;
				font-weight: 400;
				text-align: left;
				color: #141313;
			}

			.txt2 {
				position: absolute;
				top: 85rpx;
				left: 50%;
				transform: translateX(-50%);
				display: flex;
				align-items: flex-end;

				.tit11 {
					transform: translateY(-8rpx);
					margin-right: 6rpx;
					opacity: 1;
					font-size: 22rpx;
					font-family: PingFang SC, PingFang SC-Bold;
					font-weight: 700;
					text-align: right;
					color: #faa320;
				}

				.tit22 {
					opacity: 1;
					font-size: 47rpx;
					font-family: PingFang SC, PingFang SC-Bold;
					font-weight: 700;
					text-align: right;
					color: #faa320;
				}
			}

			// }
			.tit2 {
				position: absolute;
				top: 179rpx;
				left: 50%;
				transform: translateX(-50%);
				width: 572rpx;
				height: 69rpx;
				opacity: 1;
				background: linear-gradient(270deg, #ff2e46 0%, #f6bd62 100%);
				border-radius: 34rpx;
				font-size: 22rpx;
				font-family: PingFang SC, PingFang SC-Bold;
				font-weight: 700;
				text-align: center;
				line-height: 69rpx;
				color: #ffffff;
			}

		}

		.nav2 {
			width: 600rpx;
			position: absolute;
			top: 889rpx;
			left: 50%;
			transform: translateX(-50%);
			opacity: 1;
			font-size: 22rpx;
			font-family: PingFang SC, PingFang SC-Medium;
			font-weight: 500;
			text-align: center;
			color: #707070;

			/deep/ .u-news-item {
				width: 100%;
				text-align: center;
			}
		}

		.nav3 {
			z-index: 4;
			position: absolute;
			top: 970rpx;
			left: 50%;
			transform: translateX(-50%);
			width: 663rpx;
			height: 72rpx;
			opacity: 1;
			background: #fddb09;
			border-radius: 36rpx;
			font-size: 22rpx;
			font-family: PingFang SC, PingFang SC-Bold;
			font-weight: 700;
			text-align: center;
			line-height: 72rpx;
			color: #000000;
		}

		.nav4 {
			z-index: 4;
			position: absolute;
			top: 1060rpx;
			left: 50%;
			transform: translateX(-50%);
			width: 663rpx;
			height: 72rpx;
			opacity: 1;
			background: #fddb09;
			border-radius: 36rpx;
			font-size: 22rpx;
			font-family: PingFang SC, PingFang SC-Bold;
			font-weight: 700;
			text-align: center;
			line-height: 72rpx;
			color: #000000;
		}

	}

	.footer {

		// height: 857rpx;
		.nav1 {
			text-align: center;
			opacity: 1;
			font-size: 36rpx;
			font-family: SourceHanSansCN-Regular;
			color: #121212;
			margin: 20rpx 0;
		}

		.tab {
			height: 76rpx;
			display: flex;
			justify-content: space-around;

			.tab1 {
				height: 76rpx;
				line-height: 76rpx;
				opacity: 1;
				font-size: 25rpx;
				font-family: PingFang SC, PingFang SC-Regular;
				font-weight: 400;
				text-align: left;
				color: #707070;
			}

			.tab1.active {
				border-bottom: 2rpx solid #ebbfcc;
				line-height: 76rpx;
				opacity: 1;
				font-size: 25rpx;
				font-family: PingFang SC, PingFang SC-Regular;
				font-weight: 400;
				color: #ebbfcc;
			}

			.tab2 {
				height: 76rpx;
				line-height: 76rpx;
				opacity: 1;
				font-size: 25rpx;
				font-family: PingFang SC, PingFang SC-Regular;
				font-weight: 400;
				color: #707070;
			}

			.tab2.active {
				border-bottom: 2rpx solid #ebbfcc;
				line-height: 76rpx;
				opacity: 1;
				font-size: 25rpx;
				font-family: PingFang SC, PingFang SC-Regular;
				font-weight: 400;
				color: #ebbfcc;
			}
		}

		.box {
			overflow: scroll;
			width: 674rpx;
			height: 743rpx;
			opacity: 1;
			border-radius: 20rpx;

			.items {
				margin: 40rpx 26rpx 0 54rpx;

				.item {
					height: 95rpx;
					display: flex;
					align-items: center;
					// margin-bottom: 60rpx;
					border-bottom: 2rpx solid #eeeeee;

					.pic {
						width: 60rpx;
						height: 60rpx;
						opacity: 1;
						margin-right: 18rpx;
					}

					.tit {
						.tit1 {
							opacity: 1;
							font-size: 22rpx;
							font-family: PingFang SC, PingFang SC-Regular;
							font-weight: 400;
							color: #141313;
						}

						.tit2 {
							opacity: 1;
							font-size: 18rpx;
							font-family: PingFang SC, PingFang SC-Regular;
							font-weight: 400;
							color: #707070;
						}
					}
				}
			}
		}

	}

	.ljyq {
		.tit1 {
			opacity: 1;
			font-size: 29rpx;
			font-family: Source Han Sans CN, Source Han Sans CN-Medium;
			font-weight: 500;
			text-align: center;
			margin: 46rpx 0;
			color: #4a4a4a;
			line-height: 36rpx;
		}

		.tit2 {
			width: 576rpx;
			height: 125rpx;
			opacity: 1;
			border: 2rpx solid #ddd;
			border-radius: 40rpx;
			margin: 0 auto;

			.tit {
				margin-top: 45rpx;
				opacity: 1;
				font-size: 22rpx;
				font-family: PingFang SC, PingFang SC-Regular;
				font-weight: 400;
				text-align: center;
				color: #9b9b9b;
			}
		}

		.tit3 {
			opacity: 1;
			margin-top: 43rpx;
			font-size: 22rpx;
			font-family: PingFang SC, PingFang SC-Regular;
			font-weight: 400;
			text-align: center;
			color: #ebbfcc;
		}
	}

	.fxImg {
		width: 414rpx;
		height: 736rpx;
	}

	.downImg {
		font-size: 25rpx;
	}
</style>
