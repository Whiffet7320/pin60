<template>
	<view class="index">
		<view class="container">
			<u-toast ref="uToast" />
			<image class="picc" src="/static/1.png" mode=""></image>
			<view class="items">
				<view class="item" v-for="(item,i) in list" :key="i">
					<view class="nav1">{{item.createtime}}</view>
					<view class="nav2">订单号：{{item.order_no}}</view>
					<view class="nav3" v-for="(ele,index) in item.goods" :key='index'>
						<image class="pic1" src="/static/Group.png" mode=""></image>
						<image class="pic2" @tap="toMiandan(ele)" src="/static/zu57.png" mode=""></image>
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
				list: []
			}
		},
		onShow() {
			this.openid = uni.getStorageSync('openid')
			this.getData();
		},
		methods: {
			async getData() {
				const res = await this.$api.wx_userorder(this.openid, 2, 1, 50, 1);
				console.log(res)
				this.list = res.list
			},
			toMiandan(item) {
				console.log(11111)
				if (item.freeorder_count == 5) {
					uni.navigateTo({
						url: `/pages/miandan/miandan?sub_orderid=${item.sub_orderid}`
					})
				} else {
					this.$refs.uToast.show({
						title: '订单未达标',
						type: 'error',
					})
				}
			}
		},
	}
</script>

<style scoped lang="scss">
	.index {}

	.container {
		position: relative;

		.picc {
			position: fixed;
			bottom: 0;
			left: 54rpx;
			display: block;
			margin: 0 auto;
			width: 641rpx;
			height: calc(100vh - 100rpx);
			z-index: -1;
		}

		.items {
			margin: 42rpx 54rpx 0 54rpx;

			.item {
				margin-bottom: 71rpx;

				.nav1 {
					opacity: 1;
					font-size: 18rpx;
					font-family: SourceHanSansCN-Regular;
					text-align: center;
					color: #9b9b9b;
					line-height: 31rpx;
					letter-spacing: 0rpx;
				}

				.nav2 {
					margin-top: 28rpx;
					opacity: 1;
					font-size: 18rpx;
					font-family: SourceHanSansCN-Regular;
					text-align: right;
					margin-right: 62rpx;
					color: #9b9b9b;
					line-height: 31rpx;
					letter-spacing: 0rpx;
				}

				.nav3 {
					.pic1 {
						width: 92rpx;
						height: 92rpx;
						opacity: 1;
						transform: translateY(-9rpx);
					}

					.pic2 {
						margin-left: 9rpx;
						width: 478rpx;
						height: 136rpx;
						opacity: 1;
					}
				}
			}
		}
	}
</style>
