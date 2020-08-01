<template>
	<view class="container">
		<image src="https://cloud-minapp-34896.cloud.ifanrusercontent.com/1k1tS9r4MmBJVNnS.png" mode="aspectFit" class="logo"></image>
		<view class="title-1">拼车成功</view>
		<view class="title-2">
			上海LCM旭辉职场<br>
			20:20 上车
		</view>
		<view class="title-2">
			联系电话: <text class="phone" @click="makePhoneCall">18616990040</text>
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
				
			}
		},
		methods: {
			makePhoneCall () {
				uni.makePhoneCall({
				    phoneNumber: '18616990040' //仅为示例
				});
			},
			cancelOrder () {
				uni.reLaunch({
					url: '../index/index'
				})
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
