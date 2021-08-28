<template>
	<view class="content">
		<image class="logo" src="/static/logo.png"></image>
		<view class="text-area">
			<text class="title">{{title}}</text>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				title: 'Hello'
			}
		},
		onShow() {
			this.initBlueteeth()
		},
		methods: {
			/**
			 * 初始化蓝牙
			 */
			initBlueteeth(){
				uni.openBluetoothAdapter({
				  success(res) {
				    console.log('成功：'+res)
					//监听蓝牙状态
					uni.onBluetoothAdapterStateChange(function (ress) {
						console.log(ress);
					  if(!ress.available)(
						console.log('蓝牙已断开')
					  )
					})
				  },
				  fail(res){
					  console.log('初始化失败，请打开蓝牙');
				  }
				})
			},
			
		}
	}
</script>

<style>
	.content {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
	}

	.logo {
		height: 200rpx;
		width: 200rpx;
		margin-top: 200rpx;
		margin-left: auto;
		margin-right: auto;
		margin-bottom: 50rpx;
	}

	.text-area {
		display: flex;
		justify-content: center;
	}

	.title {
		font-size: 36rpx;
		color: #8f8f94;
	}
</style>
