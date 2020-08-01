<template>
	<view class="container">
		<scroll-view scroll-y="true">
			<uni-card
				title="松江区古楼公路1858弄新凯家园一期"
				mode="style"
				:is-shadow="true"
				thumbnail="https://cloud-minapp-34896.cloud.ifanrusercontent.com/1k1ovGwlAwifiXFi.png"
				extra="发车时间 2019-05-20 12:32:19"
				@click="gotoDetail"
				is-shadow="true"
			>
				<view class="line">车主：花老板   性别：小哥哥</view>
				<view class="line">你们的终点相距离：2KM   大约步行15分钟</view>
			</uni-card>
			<uni-card
				title="凯旋路1222弄都市花园"
				mode="style"
				:is-shadow="true"
				thumbnail="https://cloud-minapp-34896.cloud.ifanrusercontent.com/1k1ovFJXs5fOIJ1C.png"
				extra="发车时间 2019-05-20 12:32:19"
				@click="gotoDetail"
			>
				<view class="line">车主：陈老板   性别：小哥哥</view>
				<view class="line">你们的终点相距离：1.5KM   大约步行10分钟</view>
			</uni-card>
		</scroll-view>
	</view>
</template>

<script>
	var QQMapWX = require('../../lib/qqmap-wx-jssdk.min.js');
	export default {
		create () {
			var qqmapsdk = new QQMapWX({
			    key: 'K5PBZ-UEJWO-2AJWQ-S3KDA-B6BYQ-7CBDK' // 必填
			});
		},
		onLoad() {
			this._freshing = false;
			// setTimeout(() => {
			// 	this.triggered = true;
			// }, 1000)
		},
		data() {
			return {
				triggered: false
			};
		},
		methods: {
			gotoDetail () {
				uni.navigateTo({
					url: '../orderDetail/orderDetail'
				})
			}
		}
	}
</script>

<style lang="scss" scoped>
	.container {
		.scroll-view {
			height: 100vw;
			.line {
				font-size: 29rpx;
				color: #777679;
				margin-top: 3px;
			}
		}
	}
</style>
