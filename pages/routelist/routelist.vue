<template>
	<view class="container">
		<scroll-view scroll-y="true">
			<uni-card
				v-for="(item, index) in orderList"
				:key="index"
				:title="item.address"
				mode="style"
				:is-shadow="true"
				:thumbnail="item.map"
				:extra="item.start_time | startTimeFilter"
				@click="gotoDetail(item.id)"
				is-shadow="true"
			>
				<view class="line">车主：{{item.owner_nickname}}   性别：{{item.sex === 1 ? '小哥哥' : '小姐姐'}}</view>
				<!-- <view class="line">你们的终点相距离：2KM   大约步行15分钟</view> -->
			</uni-card>
		</scroll-view>
	</view>
</template>

<script>
	const QQMapWX = require('../../lib/qqmap-wx-jssdk.min.js');
	const qqmapsdk = new QQMapWX({
	    key: 'K5PBZ-UEJWO-2AJWQ-S3KDA-B6BYQ-7CBDK' // 必填
	});
	export default {
		create () {
			
		},
		onLoad () {
			this._freshing = false;
			// setTimeout(() => {
			// 	this.triggered = true;
			// }, 1000)
		},
		filters: {
			startTimeFilter (time) {
				const date = new Date();
				let year = date.getFullYear();
				let month = date.getMonth() + 1;
				let day = date.getDate();
				month = month > 9 ? month : '0' + month;;
				day = day > 9 ? day : '0' + day;
				return `发车时间: ${year}-${month}-${day} ${time}`
			}
		},
		onShow () {
			this.getOrderList()
			// this.calculateDistance() 这里计算距离
		},
		data() {
			return {
				triggered: false,
				orderList: [],
				qqmapsdk: qqmapsdk,
				mylatitude: 0,
				mylongitude: 0,
				myaddress: ''
			};
		},
		onLoad (options) {
			console.log(options)
			this.orderId = options.id
			this.mylatitude = options.mylatitude
			this.mylongitude = options.mylongitude
			this.myaddress = options.myaddress
		},
		methods: {
			async getOrderList () {
				try {
					let query = new uni.BaaS.Query();
					let OrderListObject = new uni.BaaS.TableObject('order_detail');
					let res = await OrderListObject.setQuery(query).find();
					console.log(res)
					this.orderList = res.data.objects;
				} catch(err) {
					console.log(err)
				}
			},
			gotoDetail (id) {
				const params = `?id=${id}&mylatitude=${this.mylatitude}&mylongitude=${this.mylongitude}&myaddress=${this.myaddress}`
				uni.navigateTo({
					url: `../orderDetail/orderDetail${params}`
				})
			},
			calculateDistance (e) {
				const _this = this
				this.qqmapsdk.calculateDistance({
					//mode: 'driving',//可选值：'driving'（驾车）、'walking'（步行），不填默认：'walking',可不填
					//from参数不填默认当前地址
					//获取表单提交的经纬度并设置from和to参数（示例为string格式）
					from: '', //若起点有数据则采用起点坐标，若为空默认当前地址
					to: '31.296086,121.515906', //终点坐标
					success: function(res) {//成功后的回调
					  console.log(res);
					  var res = res.result;
					 //  var dis = [];
					 //  for (var i = 0; i < res.elements.length; i++) {
						// dis.push(res.elements[i].distance); //将返回数据存入dis数组，
					 //  }
					 //  _this.setData({ //设置并更新distance数据
						// distance: dis
					 //  });
					},
					fail: function(error) {
					  console.error(error);
					},
					complete: function(res) {
					  console.log(res);
					}
				});
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
