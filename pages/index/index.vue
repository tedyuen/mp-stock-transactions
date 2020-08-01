<template>
	<view class="container">
		<image src="../../static/car.png" mode="aspectFit" class="logo"></image>
		<view class="form">
			<uni-list title="拼单信息确认">
			    <uni-list-item
					title="出发职场"
					:show-arrow="false">
					<template v-slot:right="">
						<picker @change="bindPickerChange" :value="companyAddressindex" :range="companyAddress">
							<view class="uni-input diy-width">{{companyAddress[companyAddressindex]}}</view>
						</picker>
					</template>
				</uni-list-item>
			    <uni-list-item 
					title="到达终点" 
					:rightText="terminalData ? terminalData.name : '选择目的地'" 
					:show-arrow="false"
					@click="selectTerminal"
				></uni-list-item>
			    <uni-list-item title="上车时间" :show-arrow="false">
					<template v-slot:right="">
						<picker mode="time" :value="time" @change="bindTimeChange">
							<view class="uni-input diy-width">{{currentDate}} {{time}}</view>
						</picker>
					</template>
				</uni-list-item>
				<uni-list-item 
					title="我的手机" 
					:show-arrow="false"
				>
					<template v-slot:right="">
						<input class="uni-input" v-model="phone"  type="number" placeholder="输入手机号" />
					</template>
				</uni-list-item>
				<uni-list-item
					v-if="terminalData != null"
					title="查看我的路线"
					@click="lookNavigation"
				>
				</uni-list-item>
			</uni-list>
			<button
				class="btn"
				hover-class="hover"
				open-type="getUserInfo"
				@getuserinfo="goNext">查找道友</button>
		</view>
	</view>
</template>

<script>
	const chooseLocation = requirePlugin('chooseLocation');
	export default {
		data() {
			const currentDate = this.getDate({
				format: true
			})
			return {
				originData: {
					name: '上海LCM旭辉职场',
					latitude: 31.243010809,
					longitude: 121.554030405,
					address: '上海市浦东新区张杨路2389弄LCM置汇旭辉广场'
				},
				terminalData: null,
				phone: '',
				time: '12:01',
				currentDate: currentDate,
				companyAddress: ['上海LCM旭辉职场'],
				companyAddressindex: 0,
				data: {
					id: 'y7kH-Yz_Yvb73YqvoEmhQoHtXVI66eP_bJLLnSnc1DU'
				}
			}
		},
		created () {
		},
		onShow () {
			this.terminalData = chooseLocation.getLocation(); // 如果点击确认选点按钮，则返回选点结果对象，否则返回null
			console.log(this.terminalData)
		},
		methods: {
			goNext (infoRes) {
				if (this.terminalData == null) {
					uni.showToast({
						title: '请选目的地',
						icon: 'none'
					})
					return
				}
				if (this.phone === '') {
					uni.showToast({
						title: '请输入手机号',
						icon: 'none'
					})
					return
				}
				console.log(infoRes)
				uni.showLoading({
				    title: '加载中'
				});
				uni.BaaS.auth.loginWithWechat(infoRes, {
					createUser: true,
					syncUserProfile: 'overwrite'
				}).then(user => {
					return user.setMobilePhone(this.phone)
				}, err => {
					uni.hideLoading();
					uni.showToast({
						title: '使用该功能需要授权用户信息',
						icon: 'none'
					})
				}).then(user => {
					console.log(user)
					uni.hideLoading();
					uni.navigateTo({
						url: '../routelist/routelist'
					})
				}).catch(e => {
					uni.hideLoading();
					uni.showToast({
						title: '重复手机号',
						icon: 'none'
					})
				})
			},
			selectTerminal () {
				const key = 'K5PBZ-UEJWO-2AJWQ-S3KDA-B6BYQ-7CBDK'; //使用在腾讯位置服务申请的key
				const referer = '奇葩顺道友'; //调用插件的app的名称
				const location = JSON.stringify({
				  latitude: 39.89631551,
				  longitude: 116.323459711
				});
				const category = '房产小区,地名地址';
				 
				uni.navigateTo({
					url: 'plugin://chooseLocation/index?key=' + key + '&referer=' + referer + '&category=' + category
				});
			},
			lookNavigation () {
				if (this.terminalData != null) {
					let plugin = requirePlugin('routePlan');
					let key = 'K5PBZ-UEJWO-2AJWQ-S3KDA-B6BYQ-7CBDK';  //使用在腾讯位置服务申请的key
					
					let referer = '奇葩顺道友';   //调用插件的app的名称
					let endPoint = JSON.stringify({  //终点
					    'name': this.terminalData.name,
					    'latitude': this.terminalData.latitude,
					    'longitude': this.terminalData.longitude
					});
					let startPoint = JSON.stringify(this.originData)
					wx.navigateTo({
					    url: 'plugin://routePlan/index?key=' + key + '&referer=' + referer + '&endPoint=' + endPoint + '&startPoint=' + startPoint
					});
				}
			},
			bindTimeChange: function(e) {
				this.time = e.target.value
			},
			bindPickerChange: function(e) {
				this.companyAddressindex = e.target.value
			},
			getDate(type) {
				const date = new Date();
				let year = date.getFullYear();
				let month = date.getMonth() + 1;
				let day = date.getDate();
	
				if (type === 'start') {
					year = year - 60;
				} else if (type === 'end') {
					year = year + 2;
				}
				month = month > 9 ? month : '0' + month;;
				day = day > 9 ? day : '0' + day;
				return `${year}-${month}-${day}`;
			}
		}
	}
</script>

<style lang="scss" scoped>
	.container {
		.logo {
			margin-top: 10vw;
			width: 40vw;
			height: 35vw;
			margin-left: 30vw;
		}
		.form {
			margin-top: 10vw;
		}
	}
	.btn {
		background-color: #fc7211;
		color: #ffffff;
		margin: 10vw;
		width:80vw;
	}
	.hover {
		background-color: rgba(252, 114, 17, 0.5);
	}
	.uni-input {
		text-align: right;
		color: #999;
		font-size: 24rpx;
	}
	.diy-width {
		width: 60vw;
	}
</style>
