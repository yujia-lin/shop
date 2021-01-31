<template>
	<view class="box">
		<view class="cardBG"><image src="../../static/0.png" class="cardBGImg" mode="widthFix"></image></view>
		<canvas canvas-id="myCanvas" id='canvasBox'></canvas>
		<view class="synthesis" id="synthesis">
			<view class="synthesisBottomBox synthesisBox" id="synthesisBottomBox"></view>
			<view class="synthesisBottomBG synthesisBox" id="synthesisBoxBG"></view>
			<image :src="imgList.pic4" mode="widthFix" class="synthesisBox synthesisTopBG" id="pic4"></image>
			<view class="synthesisBox synthesisTitle" id="company">{{information.company}}</view>
			<view class="synthesisBox synthesisName" id="name">{{information.name}}</view>
			<view class="synthesisBox synthesisPosition" id="position">{{information.position}}</view>
			<image :src="imgList.pic1" class="synthesisBox synthesisBox1 synthesisIcon" id="pic1" mode="widthFix"></image>
			<view class="synthesisBox synthesisBoxt1 synthesisText" id="text1">{{information.text1}}</view>
			<view class="synthesisBox synthesisBoxt1 synthesisValue" id="phone">{{information.phone}}</view>
			<image :src="imgList.pic2" class="synthesisBox synthesisBox2 synthesisIcon1 synthesisIcon"  id="pic2" mode="widthFix"></image>
			<view class="synthesisBox synthesisBoxt2 synthesisText"  id="text2">{{information.text2}}</view>
			<view class="synthesisBox synthesisBoxt2 synthesisValue"  id="tel">{{information.tel}}</view>
			<image :src="imgList.pic3" class="synthesisBox synthesisBox3 synthesisIcon1 synthesisIcon" id="pic3" mode="widthFix"></image>
			<view class="synthesisBox synthesisBoxt3 synthesisText"  id="text3">{{information.text3}}</view>
			<view class="synthesisBox synthesisBoxt3 synthesisValue" id="email">{{information.email}}</view>
			<image :src="imgList.pic5" mode="widthFix" class="synthesisBox synthesisPic" id="pic5"></image>
			<image :src="imgList.pic6" mode="widthFix" class="synthesisBox synthesisCode" id="pic6"></image>
		</view>
		<view class="sharePopup" v-if="shareFlag" @tap="hideShare">
			<view class="shareMain" @tap.stop="clickno">
				<button  class="shareBtn"  open-type="share" @ShareAppMessage="onShareAppMessage ">
					<view class="shareIcon shareIcon1">
						<image src="../../static/forward_icon.png" class="shareIconImg" mode="widthFix"></image>
					</view>
					<view class="shareText">转发</view>
				</button>
				<view class="shareBtn" @tap.stop="canvasSynthesis">
					<view class="shareIcon shareIcon2">
						<image src="../../static/poster_icon.png" 	class="shareIconImg" mode="widthFix"></image>
					</view>
					<view class="shareText">生成海报</view>
				</view>
			</view>
		</view>
		<view class="cardBox">
			<view class="cardhead">
				<view class="name cardheadText">{{information.name}}</view>
				<view class="position cardheadText">{{information.position}}</view>
				<view class="company cardheadText">{{information.company}}</view>
			</view>
			<view class="cardSeparate">
				<view class="cardSeparateL cardSeparateC"></view>
				<view class="cardSeparateR cardSeparateC"></view>
				<view class="cardSeparateLine"></view>
			</view>
			<view class="cardbody">
				<view class="cardDataItem f_flex" @click="goPhone(information.phone)">
					<view class="cardDataItemText">手机：</view>
					<view class="cardDataItemOutput z_flex">{{information.phone}}</view>
					<view class="cardDataItemIcon"><image src="../../static/phone_icon.png" class="cardDataItemIconImg" mode="widthFix"></image></view>
				</view>
				<view class="cardDataItem f_flex" @click="goPhone(information.tel)">
					<view class="cardDataItemText">固话：</view>
					<view class="cardDataItemOutput z_flex">{{information.tel}}</view>
					<view class="cardDataItemIcon"><image src="../../static/tel_icon.png" class="cardDataItemIconImg" mode="widthFix"></image></view>
				</view>
				<view class="cardDataItem f_flex" @click="copyText(information.email)">
					<view class="cardDataItemText">邮箱：</view>
					<view class="cardDataItemOutput z_flex">{{information.email}}</view>
					<view class="cardDataItemIcon"><image src="../../static/email_icon.png" class="cardDataItemIconImg" mode="widthFix"></image></view>
				</view>
			</view>
			<view class="cardfoot tac">
				<view class="shareShowBtn cardfootBtn" @tap="addContacts">同步通讯录</view>
				<view class="shareShowBtn cardfootBtn" @tap="showShare">分享名片</view>
			</view>
		</view>
		<view class="introduceBox">
			<view class="introduceTitle">
				<view class="introduceTitleLine"></view>
				<view class="introduceTitleText">简介</view>
			</view>
			<view class="introduceMain">{{information.introduce}}</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				deviation:0,//偏移值
				base64:[],
				information:{
					name:"欧阳峰",//姓名
					position:"总经理",//职位
					company:"云澍（厦门）工业设计有限公司",//公司
					phone:"13682890983",//公司
					tel:"88088008",//公司
					email:"840880059@qq.com",//邮箱
					introduce:"公众号运营，小程序开发、app开发"
				},
				shareToken:"",
				shareFlag:false,
				fontSize:{
					name:32,
					position:32,
					company:36,
					phone:24,
					tel:24,
					email:24,
					text2:24,
					text3:24
				},
				imgList:{
					pic1:"../../static/synthesisIcon1.png",
					pic2:"../../static/synthesisIcon2.png",
					pic3:"../../static/synthesisIcon3.png",
					pic4:"../../static/0.png",
					pic5:"../../static/pic_03.png",
				},
				startFlag:false,
				throttleFlag:false
			}
		},
		onLoad() {
			this.getcode();
			this.information.text1='手机：'
			this.information.text2='固话：'
			this.information.text3='邮箱：'
			var _this=this;
		},
		methods: {
			onShareAppMessage (res){
				//转发分享
				return {
					title: "名片",
					path: "/pages/index/index",  //页面路径及参数
					imageUrl:'',  //图片链接，必须是网络连接，后面拼接时间戳防止本地缓存
				}
			},
			addContacts:function(){
				uni.addPhoneContact({
				    firstName:this.information.name,
					email:this.information.email,
				    mobilePhoneNumber: this.information.phone,
				    success: function () {
				        console.log('success');
				    },
				    fail: function () {
				        console.log('fail');
				    }
				});
			},
			clickno:function(){
				// 阻止冒泡
			},
			getcode:function(){
				// 获取微信token用于生成小程序码
				var _this=this;
		
				var  appid="wxbf5fa3d3954f012c";
				var  secret="cd61588b5813c7bd9849be2b9d5c7562";
				 uni.request({
				        url: `https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&appid=${appid}&secret=${secret}`,
				        method: "GET",
				    }).then((res) => {
				        if (res[1].data.expires_in != 7200) {
				            uni.showToast({
				                title: "分享失败，请重新尝试。",
				                icon: "none",
				                duration: 2000
				            })
				            uni.hideLoading();
				            return
				       }
				       console.log(res)
				       _this.shareToken = res[1].data.access_token;
					   _this.getwxacodeunlimit();
				       uni.hideLoading();
				    }).catch(err => {
				        console.log(err)
				        uni.hideLoading();
				    })
			},
			getwxacodeunlimit:function(){
				// token请求小程序码
				var _this=this;
				uni.request({
				        url: `https://api.weixin.qq.com/wxa/getwxacodeunlimit?access_token=${this.shareToken}`,
				        method: "POST",
				        data: {
				             scene: "id=123&name=jack",
				             page: 'pages/index/index'
				        },
				        responseType: 'arraybuffer',
				        success: function(res) {
				            uni.hideLoading();
				            let src = wx.arrayBufferToBase64(res.data);
				            _this.imgList.pic6 = 'data:image/png;base64,' + src;
							// console.log( _this.src2)
				            // that.showQrcode = true //控制弹出框，展示二维码
				      }
				})
			},
			goPhone:function(phone){
				//拨打电话；
				uni.makePhoneCall({
				    phoneNumber: phone //仅为示例
				});
			},
			copyText:function(text){
				//复制文本至粘贴板
				// #ifdef H5
				this.$copyText(text).then(
					res => {
						uni.showToast({
							title: '复制成功'
						})
					}
				)
				// #endif
				// #ifndef H5
				uni.setClipboardData({
					data: text,
					success: () => {
						uni.showToast({
							title: '复制成功'
						})
					}
				})
				// #endif
			},
			canvasSynthesis:function(){
				var _this=this;
				if(!this.throttleFlag){
					this.throttleFlag=true;
					uni.showLoading({ //增加loading等待效
						mask:true
					})
				
					_this.copyFn();
				}
			},
			copyFn() {
				//合成图片
				var _this=this;
				
				if(!this.startFlag){
					this.startFlag=true;
					const query = uni.createSelectorQuery().in(this);
				
					uni.createSelectorQuery().selectAll('.synthesisBox').boundingClientRect(data => {  //获取canvas-dom
						var ctx = uni.createCanvasContext('myCanvas') //绑定画布
						// begin another path
						console.log(data)
						for(var i in data){
							var flag=false;
							for(var j in _this.information){
								if(j==data[i].id){
									flag=true;
									break;
								}
							}
							var left=data[i].left;
							var top=data[i].top+_this.deviation;
							var w=data[i].width;
							var h=data[i].height;
							if(flag){
								//画文案
								ctx.beginPath()
								ctx.setFillStyle('#fff')  //设置内容1的文字样式
								var tosp=h+top;
								ctx.setFontSize(h);
								ctx.setTextAlign('left')  //设置对于坐标点的对齐方式
								ctx.fillText(_this.information[data[i].id],left,tosp) //计算距离，将文字定位于图片的某处
							}else{
								
								ctx.beginPath()
								
								if(data[i].id=="synthesisBottomBox"){
									_this.deviation=-data[i].top;
									
								}else if(data[i].id=="synthesisBoxBG"){
									//加白色底色
									ctx.rect( left, top, w, h)
									ctx.setFillStyle('#fff')
									ctx.fill();
								}else{
									//画图片
									var imgurl=""
									for(var j in _this.imgList){
										if(j==data[i].id ){
											imgurl=_this.imgList[j]
											break;
										}
									}
									
									ctx.drawImage(imgurl, left, top, w, h); //填充进图片
								}
								
									
							}
						}
						ctx.draw();  //输出到画布中
						
						setTimeout(()=>{  //不加延迟的话，base64有时候会赋予undefined
							uni.canvasToTempFilePath({
								canvasId:'myCanvas',
								success: (res) => {
									//弹出海报
									this.throttleFlag=false;
									this.base64[0]=res.tempFilePath
									uni.previewImage({
										urls: this.base64, //预览的图片数组
										current: this.base64[0], //点击预览时，显示的那一张图片
										indicator:"none",
										longPressActions: {
											itemList: ['保存图片'] //长按预览图片时显示的操作菜单
										}
									})
								}
							})
							uni.hideLoading();
						},1200)
					}).exec();
				}else{
					//弹出海报
					this.throttleFlag=false;
					uni.hideLoading();
					uni.previewImage({
						urls: this.base64, //预览的图片数组
						current: this.base64[0], //点击预览时，显示的那一张图片
						indicator:"none",
						longPressActions: {
							itemList: ['保存图片'] //长按预览图片时显示的操作菜单
						}
					})
				}
			},
			hideShare:function(){
				//分享弹窗显示
				this.shareFlag=false;
			},
			showShare:function(){
				//分享弹窗隐藏
				this.shareFlag=true;
			},
		}
	}
</script>

<style>
	button::after{ border: none;} 
	.cardBGImg{
		width: 100%;
		display: block;
	}
	page{
		height: 100%;
	}
	.cardheadText{
		font-size: 36rpx;
		line-height:60rpx ;
	}
	.box{
		min-height: 100%;
		background: #EDEDED;
		padding: 30rpx;
		box-sizing: border-box;
	}
	.cardBox{
		margin-bottom: 30rpx;
		border-radius: 20rpx;
		overflow: hidden;
	}
	.cardhead{
		background: #fff;
		padding: 20rpx 0;
	}
	.cardbody{
		padding:30rpx 0 ;
		background: #fff;
	}
	.cardSeparate{
		height: 50rpx;
		background: #fff;
		width: 100%;
		position: relative;
	}
	.cardSeparateC{
		width: 50rpx;
		height: 50rpx;
		background: #EDEDED;
		border-radius: 50%;
		position: absolute;
		top: 0;
	}
	.cardSeparateL{
		left: -25rpx;
	}
	.cardSeparateR{
		right: -25rpx;
	}
	.cardSeparateLine{
		height: 23rpx;
		border-bottom:2rpx dashed #E0E0E0 ;
	}
	.introduceBox{
		background: #fff;
		border-radius: 20rpx;
	}
	.cardDataItem{
		padding: 0 18rpx;
		line-height: 64rpx;
		font-size: 28rpx;
	}
	.cardDataItemIcon{
		width: 64rpx;
		height: 64rpx;
		box-sizing: border-box;
		padding: 8rpx;
	}
	.cardDataItemIconImg{
		width: 100%;
	}
	.cardheadText{
		text-align: center;
	}
	.cardDataItemText{
		font-size: 30rpx;
	}
	.cardDataItemOutput {
		font-size: 30rpx;
	}
	.introduceTitle{
		padding: 40rpx 20rpx 30rpx;
		overflow: hidden;
		border-bottom: 1px solid #ccc;
	}
	
	.introduceTitleLine{
		width: 10rpx;
		height: 40rpx;
		background: #333;
		float: left;
		margin-right: 18rpx;
	}
	.introduceTitleText{
		font-size: 40rpx;
		line-height: 1;
		float: left;
	}
	.introduceMain{
		padding:40rpx 20rpx;
	}
	.cardfoot{
		background-color: #fff;
		padding: 20rpx 0 30rpx;
	}
	.tac{
		text-align: center;
	}
	.cardfootBtn{
		width: 260rpx;
		height: 100rpx;
		display: inline-block;
		border-radius: 20rpx;
		text-align: center;
		line-height: 100rpx;
		font-weight: 6000;
		color: #ffffff;
		text-shadow: 0 0 0 #000000, 0 0 6px #000000;
		box-shadow: 0 0 6px 1px #1c2127;
		font-size: 38rpx;
		margin: 0 20rpx;
		background-image: linear-gradient(#E8E8E8 30%,#6F6F6F 85%);
	}
	.sharePopup {
		position: fixed;
		left: 0;
		right: 0;
		top: 0;
		bottom: 0;
		z-index: 3;
		background-color: rgba(0,0,0,0.5);
	}
	.shareMain {
		padding: 80rpx 0 60rpx;
		background-color: #fff;
		position: absolute;
		bottom: 0;
		left: 0;
		right: 0;
		overflow: hidden;
	}
	.shareBtn{
		width: 50%;
		float: left;
		background: none;
	} 
	.shareIcon {
		padding: 40rpx;
		width: 140rpx;
		height: 140rpx;
		display: block;
		margin: 0 auto;
		border-radius:50% ;
		box-sizing: border-box;
	}
	.shareIconImg{
		display: block;
		width: 100%;
		height: 100%;
	}
	.shareIcon1 {
		background-color: #333;
	}
	.shareIcon2 {
		background-color: #333;
	}
	.shareText{
		line-height: 80rpx;
		font-size:40rpx;
		text-align: center;
		font-weight: 600;
	}
	.synthesis{
		position: absolute;
		width: 750rpx;
		height: 844rpx;
		top: 0;
		left: 0;
		z-index: -1;
		opacity: 0;
	}
	.synthesisTopBG{
		position: absolute;
		width: 750rpx;
		height: 422rpx;
		top: 0;
		left: 0;
	}
	.synthesisBottomBox{
		position: absolute;
		width: 750rpx;
		height: 844rpx;
		top: 0;
		left: 0;
	}
	.synthesisBottomBG{
		position: absolute;
		width: 750rpx;
		height: 422rpx;
		background: #fff;
		top: 422rpx;
		left: 0;
	}
	.synthesisPic{
		position: absolute;
		width: 314rpx;
		height: 251rpx;
		left:405.5rpx;
		top: 507.5rpx;
		z-index: 3;
	} 
	.synthesisCode{
		position: absolute;
		width: 234rpx;
		height: 234rpx;
		left:70.5rpx ;
		top:516rpx;
		z-index: 3;
	}
	.synthesisIcon {
		position: absolute;
		left:50rpx;
		z-index: 3;
		width: 44rpx;
		display: block;
	}
	.synthesisIcon1 {
		width: 40rpx;
	}
	
	.synthesisText {
	    position: absolute;
	    left: 110rpx;
	    font-size: 24rpx;
	    z-index: 3;
	    width: 108rpx;
	    color: #fff;
		line-height: 1;
	}
	.synthesisValue{
		position: absolute;
		font-size: 24rpx;
		line-height: 1;
		color: #fff;
		z-index: 3;
		left: 176rpx;
	}
	.synthesisBox1{
		top: 140rpx;
	}
	.synthesisBoxt1{
		top: 150rpx;
	}
	.synthesisBox2{
		top: 206rpx;
	}
	.synthesisBoxt2{
		top: 213rpx;
	}
	.synthesisBox3{
		top: 271rpx;
	}
	.synthesisBoxt3{
		top: 275rpx;
	}
	.synthesisTitle{
		position: absolute;
		font-size: 36rpx;
		line-height: 1;
		top: 37rpx;
		left: 26px;
		text-align: center;
		color: #fff;
		z-index: 3;
		
	}
	.synthesisName {
		position: absolute;
		left: 500rpx;
	    top: 140rpx;
	    font-size: 32rpx;
		line-height: 1;
		color: #fff;
		z-index: 3;
	}
	.synthesisPosition{
		position: absolute;
		left: 500rpx;
		top: 194rpx;
		line-height: 1;
		font-size: 32rpx;
		color: #fff;
		z-index: 3;
	}
	
	#canvasBox {
		position: absolute;
		width: 750rpx;
		height: 844rpx;
		top: -99999899rpx;
		left: -99999899rpx;
		z-index: 9999;
	}
	.immm {
		width: 750rpx;
		height: 844rpx;
	}
</style>
