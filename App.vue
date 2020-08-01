<script>
export default {
	onLaunch: function() {
		console.log('App Launch');
		uni.login({
		  provider: 'weixin',
		  success: function (loginRes) {
		    console.log("weixin: ", loginRes);
		  }
		});
		uni.BaaS = requirePlugin('sdkPlugin')
		    //让插件帮助完成登录、支付等功能
		uni.BaaS.wxExtend(uni.login,
			uni.getUserInfo,
			uni.requestPayment)
		uni.BaaS.init('df632c006ca258b3cca7')
		
		// uni.BaaS.auth.register({email: 'ted_yuen@126.com', password: '123456'}).then(user => {
		//   console.log(user)
		// }).catch(err=>{
		//   // HError 对象
		// })
		
		uni.BaaS.auth.loginWithWechat().then(user => {
			console.log(user)
		}) // 静默登录
		// uni.BaaS.auth.getCurrentUser().then(user => {
		//   // user 为 currentUser 对象
		//   console.log(user)
		// }).catch(err => {
		//   // HError
		//   if( err.code === 604 ){
		//     console.log('用户未登录')
		//   }
		// })
		// let MyTableObject = new uni.BaaS.TableObject('test')
		// let MyRecord = MyTableObject.create()
		// MyRecord.set({
		// 	test:'aaa'
		// })
		// MyRecord.save()
		// MyTableObject.get('5eca0f4f67a37b742afb6d47').then(res => {
		// 	console.log(res)
		// })
		
		// let query = new uni.BaaS.Query()
		// MyTableObject.setQuery(query).find().then(res => {
		// 	console.log(res)
		// })
	},
	onShow: function() {
		console.log('App Show');
	},
	onHide: function() {
		console.log('App Hide');
	}
};
</script>

<style>
/* 解决头条小程序组件内引入字体不生效的问题 */
/* #ifdef MP-TOUTIAO */
@font-face {
	font-family: uniicons;
	src: url('/static/uni.ttf');
}
/* #endif */
</style>
