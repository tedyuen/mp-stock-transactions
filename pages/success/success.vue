<template>
	<view class="container">
		<image src="https://cloud-minapp-34896.cloud.ifanrusercontent.com/1k1tS9r4MmBJVNnS.png" mode="aspectFit" class="logo"></image>
		<view class="title-1">拼车成功</view>
		<view class="title-2">
			上海LCM旭辉职场<br>
			<text v-if="orderDetail">{{orderDetail.start_time}} 上车</text>
		</view>
		<view class="title-2" v-if="orderDetail">
			联系电话: <text class="phone" @click="makePhoneCall">{{orderDetail.phone}}</text>
		</view>
		<view class="title-3" @click="cancelOrder">
			我要取消订单
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				orderDetail: undefined,
				user: undefined
			}
		},
		onLoad (options) {
			this.orderId = options.id
		},
		onShow () {
			uni.BaaS.auth.loginWithWechat().then(user => {
				console.log('user:', user)
				this.user = user
			})
			this.getOrderDetail()
		},
		methods: {
			async getOrderDetail () {
				try {
					let OrderListObject = new uni.BaaS.TableObject('order_detail');
					let res = await OrderListObject.get(this.orderId)
					console.log(res)
					this.orderDetail = res.data
				} catch(err) {
					console.log(err)
				}
			},
			makePhoneCall () {
				uni.makePhoneCall({
				    phoneNumber: '18616990040' //仅为示例
				});
			},
			async cancelOrder () {
				const _this = this
				uni.showModal({
					title: '提示',
					content: '确定取消订单吗',
					success: async function (res) {
						if (res.confirm) {
							try {
								uni.showLoading({
									title: '正在取消...'
								});
								const user = await uni.BaaS.auth.loginWithWechat()
								console.log('user:', user)
								let query = new uni.BaaS.Query();
								console.log(user.created_by)
								console.log(user.city)
								query.contains('order_id', _this.orderId)
								query.compare('deleted', '=', 0)
								query.compare('created_by', '=', user.created_by)
								let MemberListObject = new uni.BaaS.TableObject('order_cust');
								let res = await MemberListObject.setQuery(query).find();
								console.log(res)
								let product = MemberListObject.getWithoutData(res.data.objects[0].id)
								product.set('deleted', 1)
								await product.update()
								uni.hideLoading();
								uni.reLaunch({
									url: '../index/index'
								})
							} catch(err) {
								console.log(err)
								uni.hideLoading();
								uni.showToast({
									title: '取消失败，请重试',
									icon: 'none'
								})
							}
						} else if (res.cancel) {
							console.log('用户点击取消');
						}
					}
				});
				
			}
		}
	}
</script>

<style lang="scss" scoped>
	.container {
		text-align: center;
		.logo {
			margin-top: 20vw;
			width: 40vw;
			height: 40vw;
			// margin-left: 30vw;
		}
		.title-1 {
			margin-top: 10vw;
			margin-bottom: 10vw;
			font-size: 40rpx;
			color: #555;
		}
		.title-2 {
			margin-top: 5vw;
			font-size: 35rpx;
			color: #777;
			.phone {
				color: #FC7211;
				font-size: 38rpx;
				text-decoration: underline;
				margin-left: 2vw;
			}
		}
		.title-3 {
			margin-top: 15vw;
			color: #FC7211;
			font-size: 30rpx;
			text-decoration: underline;
		}
	}
</style>
