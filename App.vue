<script>
export default {
	onLaunch: function() {
		console.log('App Launch');
		// uni.login({
		//   provider: 'weixin',
		//   success: function (loginRes) {
		//     console.log("weixin: ", loginRes);
		//   }
		// });
		uni.BaaS = requirePlugin('sdkPlugin')
		    //让插件帮助完成登录、支付等功能
		uni.BaaS.wxExtend(uni.login,
		uni.getUserInfo,
		uni.requestPayment)

		uni.BaaS.init('a1e2b6a72d2e5f1d80ae')
		uni.BaaS.auth.loginWithWechat() // 静默登录
		console.log(111)
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
