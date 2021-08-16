<template>
	<view class="index">
		<view class="container">
			<u-toast ref="uToast" />
			<view class="nav1" :style="{'background': color}">
				<image v-if="list.postsale_status == 0" class="pic1" src="/static/lujin445.png" mode=""></image>
				<image v-if="list.postsale_status == 1" class="pic1 cg" src="/static/lujin909.png" mode=""></image>
				<image v-if="list.postsale_status == 2" class="pic1 cg" src="/static/zu706.png" mode=""></image>
				<view class="tit1">{{title}}</view>
			</view>
			<view class="nav2">
				<view class="content" v-if="list.postsale_status == 0">
					<view class="tit1">您已成功发起售后申请，请耐心等待。</view>
					<view class="tit2">
						<view class="tit2-1">
							<view class="dian"></view>
							<view class="tit2-1-1">如果商家拒绝，您可以修改售后申请后再次发起，商家会重新处理。</view>
						</view>
						<view class="tit2-2">
							<view class="dian"></view>
							<view class="tit2-2-1">如果协商后问题仍未解决，可联系客服介入。</view>
						</view>
					</view>
					<view class="tit3">
						<button class="btn1" open-type="contact" bindcontact="toKefu">
							客服介入
						</button>
						<view class="btn2" @click="chexiao">撤销申请</view>
						<view class="btn3">修改申请</view>
					</view>
				</view>
				<view class="content" v-if="list.postsale_status == 1">
					<view class="tit2">
						<view class="tit2-1">
							<view class="dian"></view>
							<view class="tit2-1-1">退款成功后，资金将自动打回原有账户，请耐心等候。</view>
						</view>
						<view class="tit2-2">
							<view class="dian"></view>
							<view class="tit2-2-1">如遇退款迟迟不到账，可联系客服介入。</view>
						</view>
					</view>
					<view class="tit3">
						<button class="btn1" open-type="contact" bindcontact="toKefu">
							客服介入
						</button>
					</view>
				</view>
				<view class="content" v-if="list.postsale_status == 2">
					<view class="tit1">商家拒绝退款：已经把吊牌摘掉了，不可以退</view>
					<view class="tit2">
						<view class="tit2-1">
							<view class="dian"></view>
							<view class="tit2-1-1">如果商家拒绝，您可以修改售后申请后再次发起，商家会重新处理。</view>
						</view>
						<view class="tit2-2">
							<view class="dian"></view>
							<view class="tit2-2-1">如果协商后问题仍未解决，可联系客服介入。</view>
						</view>
					</view>
					<view class="tit3">
						<button class="btn1" open-type="contact" bindcontact="toKefu">
							客服介入
						</button>
						<view class="btn2" @click="toZaici">再次申请</view>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				openid: '',
				order_id: '',
				title: '',
				color: '#ebbfcc',
				list: [],
			}
		},
		onLoad(option) {
			this.order_id = option.order_id;
			console.log(option)
			this.openid = uni.getStorageSync('openid')
		},
		onShow() {
			this.getData()
		},
		methods: {
			toKefu() {
				uni.navigateTo({
					url: "/pages/wode/user/lianxikefu"
				})
			},
			toZaici(){
				uni.navigateTo({
					url: `/shenqinshouhou/pages/shenqinshouhou?order_id=${this.order_id}`
				})
			},
			async getData() {
				const res = await this.$api.wx_orderspostsalesubview(this.openid, this.order_id);
				console.log(res)
				this.list = res.list
				if (res.list.postsale_status == 0) {
					this.title = '商家正在处理中...'
				} else if (res.list.postsale_status == 1) {
					this.title = '处理成功！'
					this.color = '#9bdf96'
				} else if (res.list.postsale_status == 2) {
					this.title = '商家拒绝申请'
					this.color = '#af0000'
				}
			},
			async chexiao() {
				var signstr = "openid=" + this.openid + "&order_id=" + this.order_id + "";
				const md51 = this.$md5(signstr);
				const md52 = md51 + this.$apikey;
				const md = this.$md5(md52).toUpperCase();
				const res = await this.$api.wx_orderspostsalecancel(this.openid, this.order_id, md);
				console.log(res)
				if (res.result == 1) {
					this.$refs.uToast.show({
						title: res.msg,
						type: 'success',
						duration: 500,
						url: `/wodeQuanbudingdan/pages/quanbudingdan?orders_status=-1`,
					})
				} else {
					this.$refs.uToast.show({
						title: res.msg,
						type: 'warning',
						duration: 500,
					})
				}
			},
		}
	}
</script>

<style scoped lang="scss">
	.index {}

	.container {
		.nav1 {
			height: 212rpx;
			opacity: 1;
			background: #ebbfcc;
			border-bottom-right-radius: 20rpx;
			border-bottom-left-radius: 20rpx;
			display: flex;
			align-items: center;

			.pic1 {
				margin-left: 89rpx;
				width: 29rpx;
				height: 36rpx;
				opacity: 1;
			}

			.pic1.cg {
				margin-left: 89rpx;
				width: 72rpx;
				height: 72rpx;
				opacity: 1;
			}

			.tit1 {
				margin-left: 18rpx;
				opacity: 1;
				font-size: 36rpx;
				font-family: SourceHanSansCN-Regular;
				color: #ffffff;
			}
		}

		.nav2 {
			z-index: -1;
			height: 308rpx;
			border-bottom: 2rpx solid #ececec;
			border-bottom-left-radius: 30rpx;
			border-bottom-right-radius: 30rpx;

			.content {
				margin-left: 50rpx;

				.tit1 {
					height: 82rpx;
					opacity: 1;
					font-size: 25rpx;
					font-family: PingFang SC, PingFang SC-Regular;
					font-weight: 400;
					color: #ebbfcc;
					line-height: 82rpx;
					border-bottom: 2rpx solid #ececec;
				}

				.tit2 {
					.tit2-1 {
						margin-top: 28rpx;
						display: flex;
						align-items: center;

						.dian {
							width: 9rpx;
							height: 9rpx;
							opacity: 1;
							background: #9b9b9b;
							border-radius: 50%;
						}

						.tit2-1-1 {
							margin-left: 13rpx;
							opacity: 1;
							font-size: 20rpx;
							font-family: PingFang SC, PingFang SC-Regular;
							font-weight: 400;
							text-align: left;
							color: #707070;
						}
					}

					.tit2-2 {
						margin-top: 9rpx;
						display: flex;
						align-items: center;

						.dian {
							width: 9rpx;
							height: 9rpx;
							opacity: 1;
							background: #9b9b9b;
							border-radius: 50%;
						}

						.tit2-2-1 {
							margin-left: 13rpx;
							opacity: 1;
							font-size: 20rpx;
							font-family: PingFang SC, PingFang SC-Regular;
							font-weight: 400;
							text-align: left;
							color: #707070;
						}
					}
				}

				.tit3 {
					display: flex;
					margin-top: 27rpx;

					.btn1 {
						margin: 0;
						padding: 0;
						width: 167rpx;
						height: 58rpx;
						opacity: 1;
						background: #ffffff;
						border: 2rpx solid #94c5e6;
						border-radius: 20rpx;
						font-size: 22rpx;
						font-family: PingFang SC, PingFang SC-Regular;
						font-weight: 400;
						text-align: center;
						line-height: 58rpx;
						color: #94c5e6;
					}

					.btn2 {
						margin: 0 14rpx;
						width: 167rpx;
						height: 58rpx;
						opacity: 1;
						background: #ffffff;
						border: 2rpx solid #F6BD62;
						border-radius: 20rpx;
						font-size: 22rpx;
						font-family: PingFang SC, PingFang SC-Regular;
						font-weight: 400;
						text-align: center;
						line-height: 58rpx;
						color: #F6BD62;
					}

					.btn3 {
						width: 167rpx;
						height: 58rpx;
						opacity: 1;
						background: #ffffff;
						border: 2rpx solid #EBBFCC;
						border-radius: 20rpx;
						font-size: 22rpx;
						font-family: PingFang SC, PingFang SC-Regular;
						font-weight: 400;
						text-align: center;
						line-height: 58rpx;
						color: #EBBFCC;
					}
				}

			}

		}


	}
</style>
