<template>
	<view>
		<!--文字-->
		<view>
			<view style="text-align: center;margin-top: 40upx;">
				<text style="font-size: 50upx;">{{search}}</text>
			</view>
			<view style="text-align: center;margin-top: 20upx;">
				<text style="">{{remind}}</text>
			</view>
		</view>
		<!--波浪特效-->
		<div class="container">
			<div class="wave ripple danger">
				<div class="circle"></div>
				<div class="circle"></div>
				<div class="circle"></div>
				<div class="content">
					<i class="fa fa-bell"></i>
				</div>
			</div>
			

		</div>
		
		<!--蓝牙图片-->
		<view style="width: 50%;margin-left: 40%;margin-top: -230rpx;">
			<image src="../../static/lanya.png" style="width: 200upx;height: 200upx;"></image>
		</view>
		<!--可用设备-->
		<view style="margin-top: 100rpx;margin-left: 25rpx;font-size: 45rpx;">
			{{keyong}}

		</view>
		<!--连接loding-->
		<view>
			<u-toast ref="uToast" />
		</view>

		<!--设备列表-->
		<view style="margin-bottom: 60rpx;margin-left: 25rpx;margin-right: 25rpx;">
			<u-button @click="onLink(item)" v-for="(item,i) in bleList" style="margin-bottom: 25rpx;" shape="circle"
				:ripple="true" ripple-bg-color="#99ddff">
				{{item.name}}
			</u-button>
		</view>

		<!--按钮-->
		<view>
			<u-button @click="bluetoothIsOpen()" shape="circle" size="default"
				style="color: #FFFFFF;width: 500upx;margin-bottom: 30rpx;background-color: #4a5cd0;">
				{{binding}}
			</u-button>
		</view>

		<!--提醒弹出框-->
		<view>
			<div>
				<u-popup v-model="popubShow" mode="bottom" height="auto" border-radius="15">
					<view style="text-align: center;font-size: 35upx;margin-top: 30upx;">{{remindeon}}</view>
					<view style="font-size: 32upx;margin-top: 30upx;color: #8799A3;text-align: center;">
						{{remindeon_one}}
					</view>
					<view style="margin-top: 30upx;">
						<button @click="popubShow = false">{{bu_cancel}}</button>
						<button @click="cancel()">{{bu_set}}</button>
					</view>
				</u-popup>
			</div>
		</view>

	</view>
</template>

<script>
	export default {
		data() {
			return {
				
				search: '',
				binding: '',
				remind: '',
				remindeon: '',
				remindeon_one: '',
				bu_cancel: '',
				bu_set: '',
				keyong: '',
				popubShow: false,
				linkShow: false,
				linkLoding: '正在连接',
				link_la: '正在连接',
				bleList: [], //蓝牙设备列表
				blelink: {}, //已连接的蓝牙设备
				toast_info: {},
				ble_info:''//蓝牙信息
			}
		},
		methods: {
			bluetoothIsOpen(){
					//关闭蓝牙搜索
					this.stopBluetoothDevicesDiscovery()
					uni.switchTab({
						url: '../edit'
					})
				
				
			},
			
			showToast(toast_info) {
				this.$refs.uToast.show({
					title: toast_info.title,
					type: toast_info.type,
					url: toast_info.url
				})
			},
			//连接蓝牙
			onLink(item) {
				
				var _this = this
				
				//判断当前设备是否已经连接
				if (item.deviceId == _this.blelink.deviceId && _this.blelink.connected == true) {

					//弹窗提醒已连接
					_this.toast_info = {
						'title': '当前设备已连接',
						'type': 'success'
					}
					_this.showToast(_this.toast_info)
					return
				}

				//连接前关闭已连接的设备
				if(_this.ble_info != null && _this.ble_info != ''){
					uni.closeBLEConnection({
						deviceId:_this.ble_info.deviceId,
						success() {
							console.log('蓝牙关闭成功');
						}
					})
				}
				
				
				//连接低功耗蓝牙设备
				uni.createBLEConnection({
					deviceId: item.deviceId,
					timeout: 6000,
					//成功
					success(res) {
						console.log(res);
						//连接成功,将当前设备信息存入缓存
						uni.setStorageSync('link_ble_info',item)
						_this.toast_info = {
							'title': '连接成功',
							'type': 'success',
							'isTab': true,
							'url': '../edit'
						}
						_this.showToast(_this.toast_info)

						setTimeout(() => {
							uni.switchTab({
								url: '../edit'
							})
						}, 800)




					},
					//失败
					fail() {
						_this.toast_info = {
							'title': '连接失败',
							'type': 'success',

						}
						_this.showToast(_this.toast_info)
					}

				})
				//连接成功 关闭搜索
				_this.stopBluetoothDevicesDiscovery()
			},
			//跳转到手机设置页
			cancel() {
				var main = plus.android.runtimeMainActivity();
				var Intent = plus.android.importClass("android.content.Intent");
				var mIntent = new Intent('android.settings.SETTINGS');
				main.startActivity(mIntent);
			},
			//蓝牙搜索 
			startBluetoothDeviceDiscovery() {
				console.log('进入搜索');
				//在页面显示的时候判断是都已经初始化完成蓝牙适配器若成功，则开始查找设备
				let self = this;
				setTimeout(function() {

					console.log("开始搜寻智能设备");
					uni.startBluetoothDevicesDiscovery({
						success: res => {
							self.onBluetoothDeviceFound();
						},
						fail: res => {
							console.log("查找设备失败!");
							uni.showToast({
								icon: "none",
								title: "查找设备失败！",
								duration: 3000
							})
						}
					});

				}, 300);
			},
			/**
			 * 停止搜索蓝牙设备 
			 */
			stopBluetoothDevicesDiscovery() {
				uni.stopBluetoothDevicesDiscovery({
					success: e => {
						console.log('停止搜索蓝牙设备:' + e.errMsg);
					},
					fail: e => {
						console.log('停止搜索蓝牙设备失败，错误码：' + e.errCode);
					}
				});
			},
			/**
			 * 发现外围设备
			 */
			onBluetoothDeviceFound() {
				console.log("监听寻找新设备");
				// this.getBluetoothDevices();
				uni.onBluetoothDeviceFound(devices => {
					console.log('开始监听寻找到新设备的事件');
					this.getBluetoothDevices();
				});
			},
			/**
			 * 获取在蓝牙模块生效期间所有已发现的蓝牙设备。包括已经和本机处于连接状态的设备。
			 */
			getBluetoothDevices() {
				//16进制转码
				function ab2hex(buffer) {
					const hexArr = Array.prototype.map.call(
						new Uint8Array(buffer),
						function(bit) {
							return ('00' + bit.toString(16)).slice(-2)
						}
					)
					return hexArr.join('')
				}

				var _this = this
				console.log("获取蓝牙设备");
				uni.getBluetoothDevices({
					success: res => {
						console.log('获取蓝牙设备成功:' + res.errMsg);
						console.log(res)
						// res.devices.forEach(item => {
						// 	console.log('aaa'+item.name);
						// 	if(item.name != null && item.name.trim() != ''){
						// 		_this.bleList.push()
						// 		console.log('11111111111111'+_this.bleList)
						// 		 //const c = new Map();
						// 		//_this.bleList.filter((_this.bleList) => !c.has(_this.bleList.value) && c.set(_this.bleList.value, 1));
						// 	} 
						// })
						_this.bleList = []
						var newList = res.devices
						newList.forEach(item => {
							//判断是否是需要设备
							  if (((ab2hex(item.advertisData)) == 'FFFF464C2D424D5041'.toLowerCase())) {
							  	_this.bleList.push(item)
							}

						})

					}
				});


			},


			//连接蓝牙设备


		},

		onShow() {
			console.log('进入show');
			var _this = this
			

			//判断中英文
			this.search = "正在搜索";
			this.binding = "暂不绑定"
			this.remind = "请将设备贴近手机"
			this.remindeon = "请开启蓝牙服务"
			this.remindeon_one = "开启后即可搜索和连接设备"
			this.bu_cancel = "取消"
			this.bu_set = "去设置"
			this.keyong = '可用设备:'

			//蓝牙设置
			//获取蓝牙信息
			uni.getStorage({
				key:'link_ble_info',
				success:function(res){
					if (res.data != null && res.data != '') {
						_this.ble_info = res.data
					}
				}
			})
			//蓝牙搜索
			uni.openBluetoothAdapter({
				
				success(res) {
					console.log('蓝牙初始化'+res);
					//蓝牙初始化成功,开始搜索
					_this.startBluetoothDeviceDiscovery()

				},
				fail() {
					//蓝牙初始化失败,弹窗提示打开蓝牙
					_this.popubShow = true
				}

			})
			//蓝牙开关状态监听
			uni.onBluetoothAdapterStateChange(function(res) {
				uni.setStorageSync('ava',res.available)
				if (res.available == true) {
					_this.popubShow = false
				} else {
					_this.popubShow = true
				}
			})

			//蓝牙连接状态监听
			uni.onBLEConnectionStateChange(function(res) {
				_this.blelink = res
				// 该方法回调中可以用于处理连接意外断开等异常情况
				console.log('蓝牙:' + res.deviceId + '连接状态:' + res.connected)
				uni.setStorageSync('ble_link', res);
			})
 		},

	}
</script>


<style>
	body {
		margin: 0;
	}

	.container {
		position: relative;
		top: 80upx;
		left: 25%;
		width: 40%;
		height: 40%;
	}

	.spliter {
		width: 100%;
		height: 20px;
	}

	/************以下为具体实现************/

	.wave {
		position: relative;
		width: 200px;
		height: 200px;
		text-align: center;
		line-height: 100px;
		font-size: 28px;
	}

	.wave .circle {
		position: absolute;
		border-radius: 50%;
		opacity: 0;

	}

	/* 波纹效果 */
	.wave.ripple .circle {
		width: calc(100% - 6px);
		/* 减去边框的大小 */
		height: calc(100% - 6px);
		/* 减去边框的大小 */
		border: 2px solid #2986ff;
	}

	.wave.ripple .circle:first-child {
		animation: circle-opacity 2s infinite;
	}

	.wave.ripple .circle:nth-child(2) {
		animation: circle-opacity 2s infinite;
		animation-delay: .3s;
	}

	.wave.ripple .circle:nth-child(3) {
		animation: circle-opacity 2s infinite;
		animation-delay: .6s;
	}

	.wave.ripple.danger {
		color: blule;
	}

	.wave.ripple.danger .circle {
		border-color: blule;
	}

	.wave.ripple.warning {
		color: orange;
	}

	.wave.ripple.warning .circle {
		border-color: orange;
	}

	/* 波动效果 */
	.wave.solid .circle {
		width: 100%;
		height: 100%;
		background: #fff;
	}

	.wave.solid .circle:first-child {
		animation: circle-opacity 2s infinite;
	}

	.wave.solid.danger {
		color: red;
	}

	.wave.solid.danger .circle {
		background: red;
	}

	.wave.solid.warning {
		color: orange;
	}

	.wave.solid.warning .circle {
		background: orange;
	}

	@keyframes circle-opacity {
		from {
			opacity: 1;
			transform: scale(0);
		}

		to {
			opacity: 0;
			transform: scale(1);
		}
	}
</style>
