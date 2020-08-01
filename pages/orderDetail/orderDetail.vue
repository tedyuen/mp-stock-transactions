<template>
	<view class="container">
		<uni-card is-full="true" title="Ted" thumbnail="https://media.ifanrusercontent.com/tavatar/4e/e9/4ee9f36aa55eee4ed7e1b5e1228b2d7c8b71e7f6.jpg" extra="2020-08-01 21:22" >
		    <view>
		    	<image src="https://cloud-minapp-34896.cloud.ifanrusercontent.com/1k1ovGwlAwifiXFi.png" class="top" mode="aspectFill"></image>
		    </view>
			<view class="title">订单信息</view>
			<uni-list>
			    <uni-list-item title="目的地" rightText="xxx" :show-arrow="false"/>
				<uni-list-item title="发车时间" rightText="21:20" :show-arrow="false"/>
				<uni-list-item title="总车程" rightText="20公里" :show-arrow="false"/>
				<uni-list-item title="同车人数" rightText="4人" :showArrow="false"/>
				<uni-list-item
					title="我们的终点相距"
					rightText="点击查看导航"
					note="2公里"/>
			</uni-list>
			<view class="title">通车道友</view>
			<uni-list>
			    <uni-list-item
					title="花老板"
					note="17711827384"
					thumb="https://img-cdn-qiniu.dcloud.net.cn/new-page/uni.png"
					thumb-size="lg" rightText="小哥哥" :show-arrow="false"/>
				<uni-list-item
					title="花老板"
					note="17711827384"
					thumb="https://img-cdn-qiniu.dcloud.net.cn/new-page/uni.png"
					thumb-size="lg" rightText="小哥哥" :show-arrow="false"/>
			</uni-list>
			
		</uni-card>
		
		<button
			class="btn"
			hover-class="hover"
			@click="goNext">我要拼这辆</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				
			}
		},
		methods: {
			goNext () {
				uni.navigateTo({
					url: '../success/success'
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	.container {
		.top {
			width: 100%;
			height: 30vw;
		}
		.title {
			color: #777;
			font-size: 30rpx;
			margin-top: 3vw;
			padding-left: 3vw;
			border-left: 2vw solid #FC7211;
		}
		.btn {
			background-color: #fc7211;
			color: #ffffff;
			margin: 10vw;
			margin-top: 5vw;
			width: 80vw;
		}
	}
</style>
