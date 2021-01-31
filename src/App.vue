<script>
	export default {
		onLaunch: function() {
			console.log('App Launch')
		},
		onShow: function() {
			console.log('App Show')
		},
		onHide: function() {
			console.log('App Hide')
		}
	}
</script>

<style>
	uni-page-body,#app{
		height: 100%;
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
	/*每个页面公共css */
</style>
