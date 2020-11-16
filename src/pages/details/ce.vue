<template>
	<view>
		<button @click="paymini">小程序支付</button>
		<button @click="payapp">app支付</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {

			};
		},
		methods: {

			payapp: function() {

				// 请求后台数据 prepay_id paySign nonceStr timeStamp
				uni.request({
					url: '',
					data: {
						code: code,
						payMoney: 1,
						uid: '',
						type: '1'
					},
					header: {
						'custom-header': '' //自定义请求头信息
					},
					success: (res) => {
						console.log(res);
						// 调起支付
						uni.requestPayment({
							provider: 'alipay',
							orderInfo: {
								"dealId": res.dealId,
								"appKey": res.appKey,
								"totalAmount": res.totalAmount,
								"tpOrderId": res.tpOrderId,
								"dealTitle": res.dealTitle,
								"rsaSign": res.rsaSign,
								"bizInfo": res.bizInfo
							}, //订单数据
							success: function(res) {
								console.log('success:' + JSON.stringify(res));
							},
							fail: function(err) {
								console.log('fail:' + JSON.stringify(err));
							}
						});
					}
				});


			},

			paymini: function() {
				uni.login({
					provider: 'weixin',
					success: function(loginRes) {
						let code = loginRes.code;
						// 请求后台数据 prepay_id paySign nonceStr timeStamp
						uni.request({
							url: '',
							data: {
								code: code,
								payMoney: 1,
								uid: '',
								type: '1'
							},
							header: {
								'custom-header': '' //自定义请求头信息
							},
							success: (res) => {
								console.log(res);
								// 调起支付
								uni.requestPayment({
									provider: 'wxpay',
									timeStamp: String(Date.now()),
									nonceStr: res.nonceStr,
									package: res.package,
									signType: 'MD5',
									paySign: res.paySign,
									success: function(res) {
										console.log('success:' + JSON.stringify(res));
									},
									fail: function(err) {
										console.log('fail:' + JSON.stringify(err));
									}
								});
							}
						});
					}
				});
			}
		}
	}
</script>

<style>

</style>
