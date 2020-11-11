<script>
	var api = require('./config/api.js')
	export default {
		onLaunch: function() {
			console.log('App Launch')
			//#ifdef H5
			var that=this;
			const url = encodeURIComponent(window.location.href.split('#')[0]);
			var data={
				url:url
			}
			uni.request({
				url:api.getShareSign,
				method:"GET",
				data:data,
				header:{
					'content-type':'application/x-www-form-urlencoded',
				},
				success: (res) => {
					var datas=res.data;
					that.$wx.config({
						debug: false, // 开启调试模式,调用的所有api的返回值会在客户端alert出来，若要查看传入的参数，可以在pc端打开，参数信息会通过log打出，仅在pc端时才会打印。
						appId: datas["appId"], // 必填，公众号的唯一标识
						timestamp: datas["timestamp"], // 必填，生成签名的时间戳
						nonceStr: datas["nonceStr"], // 必填，生成签名的随机串
						signature: datas["signature"],// 必填，签名
						jsApiList: ['checkJsApi','onMenuShareAppMessage', 'onMenuShareTimeline'] // 必填，需要使用的JS接口列表
					});
					that.$wx.ready(function () {
						that.$wx.onMenuShareTimeline({
						   title: "小诺万用计划书", // 分享标题
						   link:  window.location.href, // 分享链接
						   imgUrl:"https://www.gomyorder.cn/logo.png", // 分享图标
						   success: function () { 
							   // 用户确认分享后执行的回调函数
						   },
						   cancel: function () { 
							 // 用户取消分享后执行的回调函数
						   }
						});
						that.$wx.onMenuShareAppMessage({
							title: "小诺万用计划书", // 分享标题
							desc:"复合肥呼呼",
							link:  window.location.href, // 分享链接
							imgUrl:"https://www.gomyorder.cn/logo.png", // 分享图标
							success: function () { 
														   // 用户确认分享后执行的回调函数
							},
							cancel: function () { 
														 // 用户取消分享后执行的回调函数
							}
						});
					});
				}
			});
			//#endif
		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		},
		computed:{

		},
		methods: {

		}
	}
</script>

<style>
	uni-page-body,#app{
			height: 100%;
			} 
	.tac{
		text-align: center;
	}
	.clear {
		zoom: 1;
	}
	.ui-fl{
		float: left;
	}
	.ui-fr{
		float: right;
	}
	.clear:after {
		display: block;
		content: '';
		clear: both;
	}
	.f_flex {
		display: -webkit-box;
		display: -webkit-flex;
		display: flex;
	}
	
	.z_flex {
		-webkit-box-flex: 1;
		-webkit-flex: 1;
		flex: 1;
	}
	.userColorBlock {
	    height: 20rpx;
	    background: #F8F9FB;
	}
	/*每个页面公共css */
</style>
