<template>
	<view class="index">
		<image class="pic" src="/static/mcz1.png" mode=""></image>
		<view class="btn" @click="toMiandan2">
			<view class="tit1">点击进入</view>
			<u-icon class="tit2" color="#af0000" name="arrow-right"></u-icon>
		</view>
		<view class="txt1">拼单简介-抢红包流程</view>
		<view class="txt2">点击进入页面</view>
		<view class="txt3">选择免单订单可查看是否中奖</view>
		<u-tabbar height=98 active-color="#EBBFCC" mid-button-size=39 v-model="current" :list="tabbarlist"
			:mid-button="true"></u-tabbar>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				openid:'',
				tabbarlist: [{
						iconPath: "/static/shouye.png",
						selectedIconPath: "/static/shouye-active.png",
						text: '首页',
						pagePath: "/pages/index/index"
					},
					{
						midButton: true,
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
				current: 1
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
		onShow() {
			this.openid = uni.getStorageSync('openid')
			this.getData();
		},
		methods: {
			async getData(){
				const res = await this.$api.wx_userorder(this.openid,2,1,50,1);
				console.log(res)
				this.list = res.list
			},
			goTo() {
				uni.navigateTo({
					url: '/pages/miandan/miandan'
				})
			},
			toMiandan2(){
				uni.navigateTo({
					url: '/pages/freeCharge/miandantwo'
				})
			}
		}
	}
</script>

<style scoped lang="scss">
	// page{
	// 	height: 100;
	// }
	.index {
		height: 100vh;
		position: relative;
		.pic{
			position: absolute;
			top: 0;
			left: 0;
			width: 750rpx;
			height: 100%;
			opacity: 1;
		}
		.btn{
			position: absolute;
			top: 48.2%;
			left: 50%;
			transform: translate(-50%,0);
			width: 549rpx;
			height: 90rpx;
			opacity: 1;
			background: linear-gradient(97deg,#ffe9c5 18%, #ffd88a 91%);
			border-radius: 42rpx;
			display: flex;
			align-items: center;
			.tit1{
				opacity: 1;
				font-size: 29rpx;
				font-family: PingFang SC, PingFang SC-Bold;
				font-weight: 700;
				color: #af0000;
				line-height: 90rpx;
				margin-left: 217rpx;
			}
				
			.tit2{
				margin-left: 112rpx;
			}
		}
		.txt1{
			position: absolute;
			top: 62%;
			left: 50%;
			opacity: 1;
			font-size: 29rpx;
			font-family: PingFang SC, PingFang SC-Heavy;
			font-weight: 800;
			color: #af0000;
			transform: translate(-50%,0);
		}
		.txt2{
			position: absolute;
			top: 69.8%;
			left: 50%;
			font-size: 22rpx;
			transform: translate(-50%,0);
			font-weight: 800;
		}
		.txt3{
			position: absolute;
			top: 75.2%;
			left: 50%;
			font-size: 22rpx;
			transform: translate(-50%,0);
			font-weight: 800;
		}
	}
	/deep/ .u-tabbar__content__circle__border{
		display: none !important;
	}
</style>
