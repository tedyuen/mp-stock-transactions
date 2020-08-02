<template>
	<view class="container">
		<uni-card v-if="orderDetail" is-full="true" :title="orderDetail.owner_nickname" :thumbnail="orderDetail.avatar" extra="发起人" >
		    <view>
				<map style="width: 100%; height: 30vwx;"
					:markers="covers"
					:latitude="latitude"
					:longitude="longitude"
					@click="lookNavigation"
					>
				</map>
		    	<!-- <image src="https://cloud-minapp-34896.cloud.ifanrusercontent.com/1k1ovGwlAwifiXFi.png" class="top" mode="aspectFill"></image> -->
		    </view>
			<view class="title">订单信息</view>
			<uni-list v-if="orderDetail">
			    <uni-list-item title="目的地" :rightText="orderDetail.address" :show-arrow="false"/>
			    <uni-list-item title="上车时间" :rightText="orderDetail ? orderDetail.start_time : '' | startTimeFilter" :show-arrow="false"/>
				<!-- <uni-list-item title="总车程" rightText="20公里" :show-arrow="false"/> -->
				<uni-list-item
					@click="lookNavigation2"
					title="我们的终点"
					rightText="点击查看导航"/>
			</uni-list>
			<view class="title" v-if="memberListResult.length > 0">通车道友</view>
			<uni-list v-if="memberListResult.length > 0">
			    <uni-list-item
					v-for="item in memberListResult"
					:key="item.id"
					:title="item.nickname"
					:note="item.phone"
					:thumb="item.avatar"
					thumb-size="lg" :rightText="item.gender === 1 ? '小哥哥' : '小姐姐'" :show-arrow="false"/>
			</uni-list>
		</uni-card>
		<form @submit="formSubmit" report-submit="true">
			<button
				class="btn"
				hover-class="hover"
				form-type="submit"
				>我要拼这辆</button>
		</form>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				orderId: 0,
				orderDetail: undefined,
				memberList: [],
				memberListResult: [],
				latitude: 39.909,
				longitude: 116.39742,
				covers: [],
				originData: {
					name: '上海LCM旭辉职场',
					latitude: 31.243010809,
					longitude: 121.554030405,
					address: '上海市浦东新区张杨路2389弄LCM置汇旭辉广场'
				},
				mylatitude: 0,
				mylongitude: 0,
				tempId: 'y7kH-Yz_Yvb73YqvoEmhQl8JIuJHZnrF1ChOIkivbDk',
				mylatitude: 0,
				mylongitude: 0,
				myaddress: ''
			}
		},
		filters: {
			startTimeFilter (time) {
				const date = new Date();
				let year = date.getFullYear();
				let month = date.getMonth() + 1;
				let day = date.getDate();
				month = month > 9 ? month : '0' + month;;
				day = day > 9 ? day : '0' + day;
				return `${year}-${month}-${day} ${time}`
			}
		},
		onLoad (options) {
			console.log('options:', options)
			this.orderId = options.id
			this.mylatitude = options.mylatitude
			this.mylongitude = options.mylongitude
			this.myaddress = options.myaddress
		},
		onShow () {
			this.getOrderDetail()
			this.getMemberList()
		},
		methods: {
			async getOrderDetail () {
				try {
					let OrderListObject = new uni.BaaS.TableObject('order_detail');
					let res = await OrderListObject.get(this.orderId)
					console.log(res)
					this.orderDetail = res.data
					
					this.latitude = this.orderDetail.latitude
					this.longitude = this.orderDetail.longitude
					this.covers.push({
						latitude: this.orderDetail.latitude,
						longitude: this.orderDetail.longitude,
						iconPath: '../../static/location.png'
					})
				} catch(err) {
					console.log(err)
				}
			},
			async getMemberList () {
				try {
					let query = new uni.BaaS.Query();
					query.contains('order_id', this.orderId)
					query.compare('deleted', '=', 0)
					let MemberListObject = new uni.BaaS.TableObject('order_cust');
					let res = await MemberListObject.setQuery(query).find({
						withCount: true
					});
					console.log('member', res)
					this.memberList = res.data.objects
					this.setMemberData()
				} catch(err) {
					console.log(err)
				}
			},
			async setMemberData () {
				this.memberListResult = []
				const UserObject = new uni.BaaS.TableObject('_userprofile');
				for (let item of this.memberList) {
					let res = await UserObject.get(item.created_by);
					let temp = {
						...res.data,
						...item
					}
					console.log('user2', temp)
					this.memberListResult.push(temp)
				}
			},
			formSubmit (e) {
				console.log(e)
				this.goNext()
			},
			goNext () {
				uni.requestSubscribeMessage({
					tmplIds: [this.tempId],
					success: (res) => {
						console.log('1:', res)
						let subscription = []
						if (res[this.tempId] === 'accept') {
								subscription.push({
								template_id: this.tempId,
								subscription_type: 'once',
							})
						}
						uni.BaaS.subscribeMessage({subscription}).then(res => {
							console.log('2:', res)
						  // success
						}, err => {
							console.log('3:', err)
						  // fail
						})
					},
					fail: (err) => {
						console.log('4:', err)
					},
					complete: () => {
						this.goNext2()
					}
				})
			},
			goNext2 () {
				uni.showLoading({
				    title: '加载中'
				});
				const date = new Date();
				let year = date.getFullYear();
				let month = date.getMonth() + 1;
				let day = date.getDate();
				month = month > 9 ? month : '0' + month;;
				day = day > 9 ? day : '0' + day;
				
				let MyTableObject = new uni.BaaS.TableObject('order_cust')
				let MyRecord = MyTableObject.create()
				
				MyRecord.set({
					deleted: 0,
					get_on_date_time: `${year}年${month}月${day}日 ${this.orderDetail.start_time}`,
					order_id: this.orderId,
					remark: `发起人手机: ${this.orderDetail.phone}`
				})
				MyRecord.save()
				uni.hideLoading();
				const params = `?id=${this.orderId}`
				uni.reLaunch({
					url: `../success/success${params}`
				})
			},
			lookNavigation () {
				if (this.orderDetail != null) {
					let plugin = requirePlugin('routePlan');
					let key = 'K5PBZ-UEJWO-2AJWQ-S3KDA-B6BYQ-7CBDK';  //使用在腾讯位置服务申请的key
					
					let referer = '奇葩顺道友';   //调用插件的app的名称
					let endPoint = JSON.stringify({  //终点
					    'name': this.orderDetail.address_name,
					    'latitude': this.orderDetail.latitude,
					    'longitude': this.orderDetail.longitude
					});
					let startPoint = JSON.stringify(this.originData)
					wx.navigateTo({
					    url: 'plugin://routePlan/index?key=' + key + '&referer=' + referer + '&endPoint=' + endPoint + '&startPoint=' + startPoint
					});
				}
			},
			lookNavigation2 () {
				if (this.orderDetail != null) {
					let plugin = requirePlugin('routePlan');
					let key = 'K5PBZ-UEJWO-2AJWQ-S3KDA-B6BYQ-7CBDK';  //使用在腾讯位置服务申请的key
					
					let referer = '奇葩顺道友';   //调用插件的app的名称
					let startPoint = JSON.stringify({  //起点
					    'name': this.orderDetail.address,
					    'latitude': this.orderDetail.latitude,
					    'longitude': this.orderDetail.longitude
					});
					let endPoint = JSON.stringify({  //终点
					    'name': this.myaddress,
					    'latitude': this.mylatitude,
					    'longitude': this.mylongitude
					});
					wx.navigateTo({
					    url: 'plugin://routePlan/index?key=' + key + '&referer=' + referer + '&endPoint=' + endPoint + '&startPoint=' + startPoint
					});
				}
			},
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
