<template>
	<view class="index">
		<view class="container">
			<image class="pic" src="/static/251.png" mode=""></image>
			<view class="tit">使用微信授权登录后才允许操作哦～</view>
		</view>
		<!-- 		<button open-type='getUserInfo' withCredentials="true" lang="zh_CN" @getuserinfo="login" class="btn">
			点击授权
		</button> -->
		<button @click="login" class="btn">
			点击授权
		</button>
		<u-toast ref="uToast" />
	</view>
</template>

<script>
	export default {
		data() {
			return {
				code: null,
				recommend_userid: null,
				goods_id: null,
			}
		},
		onLoad(option) {
			console.log(option)
			this.goods_id = option.goods_id;
			this.recommend_userid = option.recommend_userid
		},
		methods: {
			login() {
				const that = this;
				if (uni.getSystemInfoSync().platform === 'android' || uni.getSystemInfoSync().platform == 'ios') {
					console.log('android')
					uni.login({
						provider: 'weixin',
						success: async function(loginRes) {
							var code = loginRes.code;
							uni.getUserInfo({
								provider: 'weixin',
								success: async function(infoRes) {
									console.log(infoRes, 2222222)
									try {
										var signstr = "openid=" + infoRes.userInfo.openId +
											"&avatarurl=" +
											infoRes.userInfo.avatarUrl + "&nickname=" + infoRes
											.userInfo.nickName + "&unionid=" + infoRes.userInfo
											.unionId + "";
										const md51 = that.$md5(signstr);
										const md52 = md51 + that.$apikey;
										const md = that.$md5(md52).toUpperCase()
										const res = await that.$api.app_wxlogin({
											openid: infoRes.userInfo.openId,
											avatarurl: infoRes.userInfo.avatarUrl,
											nickname: infoRes.userInfo.nickName,
											unionid: infoRes.userInfo.unionId,
											sign: md,
										})
										console.log(res, '授权')
										// var signstr = "userid=" + res.userid + "&nickname=" +
										// 	infoResss.userInfo.nickName + "&avatarurl=" + infoResss
										// 	.userInfo.avatarUrl + "";
										// const md51 = that.$md5(signstr);
										// const md52 = md51 + that.$apikey;
										// const md = that.$md5(md52).toUpperCase()
										// const res2 = await that.$api.wx_loginuserinfo({
										// 	userid: res.userid,
										// 	nickname: infoResss.userInfo.nickName,
										// 	avatarurl: infoResss.userInfo.avatarUrl,
										// 	sign: md,
										// })
										// console.log(res2)
										if (res.result == 1) {
											uni.setStorage({
												key: 'user',
												data: {
													nickname: res.nickname,
													avatarUrl: res
														.avatarUrl
												},
											})
											uni.setStorage({
												key: 'openid',
												data: res.openid,
												async success() {
													that.$refs.uToast.show({
														title: res
															.msg,
														type: 'success',
														url: '/pages/wode/wode',
														isTab: true
													})
												}
											})
										} else {
											that.$refs.uToast.show({
												title: res.msg,
												type: 'error',
											})
										}
									} catch (err) {
										console.log(err, 'err')
									}

								}
							})
						}
					})
				} else {
					console.log('小程序')
					uni.getUserProfile({
						desc: '登录',
						success: function(infoResss) {
							console.log(infoResss, 'cycy')
							uni.login({
								provider: 'weixin',
								success: async function(loginRes) {
									var code = loginRes.code;
									// console.log(code)
									uni.getUserInfo({
										provider: 'weixin',
										success: async function(infoRes) {
											try {
												const res = await that.$api.wx_login(
													code,
													infoRes.rawData,
													infoRes.iv,
													infoRes.signature,
													infoRes.encryptedData
												)
												console.log(res, '授权')
												var signstr = "userid=" + res.userid +
													"&nickname=" + infoResss.userInfo
													.nickName + "&avatarurl=" +
													infoResss
													.userInfo.avatarUrl + "";
												const md51 = that.$md5(signstr);
												const md52 = md51 + that.$apikey;
												const md = that.$md5(md52)
													.toUpperCase()
												const res2 = await that.$api
													.wx_loginuserinfo({
														userid: res.userid,
														nickname: infoResss
															.userInfo
															.nickName,
														avatarurl: infoResss
															.userInfo
															.avatarUrl,
														sign: md,
													})
												console.log(res2)
												if (res.result == 1) {
													uni.setStorage({
														key: 'user',
														data: {
															nickname: res
																.nickname,
															avatarUrl: res
																.avatarUrl
														},
													})
													uni.setStorage({
														key: 'openid',
														data: res.openid,
														async success() {
															var signstr =
																"openid=" +
																res
																.openid +
																"&recommend_userid=" +
																that
																.recommend_userid +
																"";
															const md51 =
																that
																.$md5(
																	signstr
																);
															const md52 =
																md51 +
																that
																.$apikey;
															const md = that
																.$md5(md52)
																.toUpperCase()
															const resp =
																await that
																.$api
																.wx_userrecommend(
																	res
																	.openid,
																	that
																	.recommend_userid,
																	md
																)
															console.log(
																resp)
															if (that
																.goods_id
															) {
																// 详情页邀请
																if (resp
																	.result ==
																	1) {
																	that.$refs
																		.uToast
																		.show({
																			title: res
																				.msg,
																			type: 'success',
																			// url: '/pages/shangpinxiangqin/shangpinxiangqin',
																			// params: {
																			// 	id: that.goods_id,
																			// },
																			back: true,
																		})
																} else {
																	that.$refs
																		.uToast
																		.show({
																			title: resp
																				.msg,
																			type: 'success',
																		})
																}

															} else if (that
																.recommend_userid
															) {
																// 主页邀请
																if (resp
																	.result ==
																	1) {
																	that.$refs
																		.uToast
																		.show({
																			title: res
																				.msg,
																			type: 'success',
																			url: '/pages/index/index',
																			isTab: true
																		})
																} else {
																	that.$refs
																		.uToast
																		.show({
																			title: resp
																				.msg,
																			type: 'success',
																		})
																}
															} else {
																that.$refs
																	.uToast
																	.show({
																		title: res
																			.msg,
																		type: 'success',
																		url: '/pages/wode/wode',
																		isTab: true
																	})
															}

															// uni.switchTab({
															// 	url:'/pages/wode/wode'
															// })
														}
													})
												} else {
													that.$refs.uToast.show({
														title: res.msg,
														type: 'error',
													})
												}
											} catch (err) {
												console.log(err, 'err')
											}

										}
									})
								}
							})
						}
					});
				}

			},
		}
	}
</script>

<style scoped lang="scss">
	page {
		background: #f8f8f8;
	}

	.index {}

	.container {
		width: 750rpx;
		height: 458rpx;
		opacity: 1;
		background: #ffffff;
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;

		.pic {
			width: 120rpx;
			height: 120rpx;
			opacity: 1;
		}

		.tit {
			margin-top: 40rpx;
			opacity: 1;
			font-size: 18rpx;
			font-family: SourceHanSansCN-Regular;
			text-align: left;
			color: #000000;
		}
	}

	.btn {
		margin: 63rpx auto;
		width: 428rpx;
		height: 80rpx;
		opacity: 1;
		background: #ebbfcc;
		border-radius: 18rpx;
		font-size: 22rpx;
		font-family: PingFang SC, PingFang SC-Bold;
		font-weight: 700;
		text-align: center;
		line-height: 80rpx;
		color: #ffffff;
	}
</style>
