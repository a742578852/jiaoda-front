<template>
	<view style="width: 95%;">
		<view class='cell'>
			<text>当前连接设备：</text>
			<text>{{linked}}</text>
		</view>
		<u-line color="gray" margin='20rpx'/>
		<!-- 加速度计 -->
		<view class='cell'>
			<text>加速度计</text>
			<u-switch v-model="checked1" ></u-switch>
		</view>
		<view class="sliders" v-if="checked1">
			<text>采集频率(Hz):{{value1*25}}</text>
			<view class="slider" >
				<u-slider v-model="value1" min='1' ></u-slider>
			</view>
		</view>
		<u-line color="gray" margin='20rpx'/>
		<!-- 陀螺仪 -->
		<view class='cell'>
			<text>陀螺仪</text>
			<u-switch v-model="checked2" ></u-switch>
			
		</view>
		<view class="sliders" v-if="checked2">
			<text>采集频率(Hz):{{value2*25}}</text>
			<view class="slider" >
				<u-slider v-model="value2" min='1'></u-slider>
			</view>
		</view>
		<u-line color="gray" margin='20rpx'/>
		<!-- 磁力计 -->
		<view class='cell'>
			<text>磁力计</text>
			<u-switch v-model="checked3" ></u-switch>
			
		</view>
		<view class="sliders" v-if="checked3">
			<text>采集频率(Hz):{{value3*25}}</text>
			<view class="slider" >
				<u-slider v-model="value3" min='1'></u-slider>
			</view>
		</view>
		<u-line color="gray" margin='20rpx'/>
		
		<!-- 光学传感器 -->
		<view class='cell'>
			<text>光学传感器</text>
			<u-switch v-model="checked4" ></u-switch>
		</view>
		<view class="cells" style="display: flex;justify-content: space-around;" v-if="checked4">
		<view class='cell'>
			<text style="margin-right: 10upx;">红光</text>
			<u-switch v-model="checked5" ></u-switch>
		</view>
		<view class='cell'>
			<text style="margin-right: 10upx;">红外</text>
			<u-switch v-model="checked6" ></u-switch>
		</view>
		</view>
		<view class="cells" style="display: flex;justify-content: space-around;" v-if="checked4">
		<view class='cell'>
			<text style="margin-right: 10upx;">绿光</text>
			<u-switch v-model="checked7" ></u-switch>
		</view>
		<view class='cell'>
			<text style="margin-right: 10upx;">蓝光</text>
			<u-switch v-model="checked8" ></u-switch>
		</view>
		</view>
		<view class="sliders" v-if="checked4">
			<text>采集频率(Hz):{{value4*25}}</text>
			<view class="slider" >
				<u-slider v-model="value4" min='1'></u-slider>
			</view>
		</view>
		<u-line color="gray" margin='20rpx'/>
		
		<!-- 1-生物电势AFE -->
		<view class='cell'>
			<text>1-生物电势AFE</text>
			<u-switch v-model="checked9" ></u-switch>
			
		</view>
		<view class="sliders" v-if="checked9">
			<text>工作通道数:{{value9/12.5}}</text>
			<view class="slider" >
				<u-slider v-model="value9"  step="12.5" min='12.5'></u-slider>
			</view>
		</view>
		<view class="sliders" v-if="checked9">
			<text>采集频率(Hz):{{value10*25}}</text>
			<view class="slider" >
				<u-slider v-model="value10" min='1'></u-slider>
			</view>
		</view>
		<u-line color="gray" margin='20rpx'/>
		
		<!-- 2-生物电势AFE -->
		<view class='cell'>
			<text>2-生物电势AFE</text>
			<u-switch v-model="checked10" ></u-switch>
			
		</view>
		<view class="sliders" v-if="checked10">
			<text>工作通道数:{{value11/12.5}}</text>
			<view class="slider" >
				<u-slider v-model="value11"  step="12.5" min='12.5'></u-slider>
			</view>
		</view>
		<view class="sliders" v-if="checked10">
			<text>采集频率(Hz):{{value12*25}}</text>
			<view class="slider" >
				<u-slider v-model="value12" min='1'></u-slider>
			</view>
		</view>
		<u-line color="gray" margin='20rpx'/>
			<u-button type="success" size='medium' style='margin-left:35% ;' @click="toData">确定</u-button>
			<view>
				<u-toast ref="uToast" />
			</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				linked:'当前无连接',
				checked1: false,
				checked2: false,
				checked3: false,
				checked4: false,
				checked5: false,
				checked6: false,
				checked7: false,
				checked8: false,
				checked9: false,
				checked10: false,
				value1:1,
				value2:1,
				value3:1,
				value4:1,
				value9:12.5,
				value10:1,
				value11:12.5,
				value12:1,
				uToast_info:{},
				serviceId: '', //服务id
				ble_info: '' //deviceid
			}
		},
		methods: {
			//弹窗提示
			showToast(uToast_info) {
				this.$refs.uToast.show({
					title: uToast_info.title,
					type: uToast_info.type,
					url: uToast_info.url
				})
			},
			//下发加速度计频率
			jsdjVals(){
				var _this = this
				//获取频率转换成毫秒
				if(uni.getStorageSync('value1')!='' && uni.getStorageSync('value1')!=null){
					var jsval = 1/uni.getStorageSync('value1')*25*1000
				}else{
					var jsval = 1/25*1000
				}
				//转16进制
				var jsdjVal = jsval.toString(16)
				//延时写入
				setTimeout(() => {
					uni.getBLEDeviceCharacteristics({
						deviceId: _this.ble_info.deviceId,
						serviceId: _this.serviceId,
						success(res) {
							console.log(res);
							res.characteristics.forEach((item) => {
								if (item.uuid.indexOf('8653000C') != -1) {
									const buffer = new ArrayBuffer(2)
									const dataView = new DataView(buffer)
									dataView.setUint8(0,0)
									dataView.setUint8(0,1)
									for(var i= 0;i<jsdjVal.length;i++){
										dataView.setUint8(1,jsdjVal[i])
									}
									uni.writeBLECharacteristicValue({
										deviceId: _this.ble_info.deviceId,
										serviceId: _this.serviceId,
										characteristicId: item.uuid,
										value: buffer,
										success(res) {
											var toast_info = {
												'title': '设置成功',
												'type': 'success',
											}
											_this.showToast(toast_info)
										},
										fail(res) {
											var toast_info = {
												'title': '设备写入数据失败,请检查蓝牙连接',
												'type': 'warning',
											}
											_this.showToast(toast_info)
										}
									})
								}
							})
						},
						fail(res) {
							var toast_info = {
								'title': '写入失败',
								'type': 'warning',
							}
							_this.showToast(toast_info)
							console.log(res);
						}
					})
				}, 1000)
			},
			//下发陀螺仪频率
			tlyVals(){
				var _this = this
				//获取频率转换成毫秒
				if(uni.getStorageSync('value2')!='' && uni.getStorageSync('value2')!=null){
					var jsval = 1/uni.getStorageSync('value2')*25*1000
				}else{
					var jsval = 1/25*1000
				}
				//转16进制
				var jsdjVal = jsval.toString(16)
				//延时写入
				setTimeout(() => {
					uni.getBLEDeviceCharacteristics({
						deviceId: _this.ble_info.deviceId,
						serviceId: _this.serviceId,
						success(res) {
							console.log(res);
							res.characteristics.forEach((item) => {
								if (item.uuid.indexOf('8653000C') != -1) {
									const buffer = new ArrayBuffer(2)
									const dataView = new DataView(buffer)
									dataView.setUint8(0,0)
									dataView.setUint8(0,1)
									for(var i= 0;i<jsdjVal.length;i++){
										dataView.setUint8(1,jsdjVal[i])
									}
									uni.writeBLECharacteristicValue({
										deviceId: _this.ble_info.deviceId,
										serviceId: _this.serviceId,
										characteristicId: item.uuid,
										value: buffer,
										success(res) {
											var toast_info = {
												'title': '设置成功',
												'type': 'success',
											}
											_this.showToast(toast_info)
										},
										fail(res) {
											var toast_info = {
												'title': '设备写入数据失败,请检查蓝牙连接',
												'type': 'warning',
											}
											_this.showToast(toast_info)
										}
									})
								}
							})
						},
						fail(res) {
							var toast_info = {
								'title': '写入失败',
								'type': 'warning',
							}
							_this.showToast(toast_info)
							console.log(res);
						}
					})
				}, 1000)
			},
			//下发加磁力计频率
			cljVals(){
				var _this = this
				//获取频率转换成毫秒
				if(uni.getStorageSync('value3')!='' && uni.getStorageSync('value3')!=null){
					var jsval = 1/uni.getStorageSync('value3')*25*1000
				}else{
					var jsval = 1/25*1000
				}
				//转16进制
				var jsdjVal = jsval.toString(16)
				//延时写入
				setTimeout(() => {
					uni.getBLEDeviceCharacteristics({
						deviceId: _this.ble_info.deviceId,
						serviceId: _this.serviceId,
						success(res) {
							console.log(res);
							res.characteristics.forEach((item) => {
								if (item.uuid.indexOf('8653000C') != -1) {
									const buffer = new ArrayBuffer(2)
									const dataView = new DataView(buffer)
									dataView.setUint8(0,0)
									dataView.setUint8(0,1)
									for(var i= 0;i<jsdjVal.length;i++){
										dataView.setUint8(1,jsdjVal[i])
									}
									uni.writeBLECharacteristicValue({
										deviceId: _this.ble_info.deviceId,
										serviceId: _this.serviceId,
										characteristicId: item.uuid,
										value: buffer,
										success(res) {
											var toast_info = {
												'title': '设置成功',
												'type': 'success',
											}
											_this.showToast(toast_info)
										},
										fail(res) {
											var toast_info = {
												'title': '设备写入数据失败,请检查蓝牙连接',
												'type': 'warning',
											}
											_this.showToast(toast_info)
										}
									})
								}
							})
						},
						fail(res) {
							var toast_info = {
								'title': '写入失败',
								'type': 'warning',
							}
							_this.showToast(toast_info)
							console.log(res);
						}
					})
				}, 1000)
			},
			//下发光学传感器频率
			gxcgqVals(){
				var _this = this
				//获取频率转换成毫秒
				if(uni.getStorageSync('value4')!='' && uni.getStorageSync('value4')!=null){
					var jsval = 1/uni.getStorageSync('value4')*25*1000
				}else{
					var jsval = 1/25*1000
				}
				//转16进制
				var jsdjVal = jsval.toString(16)
				//延时写入
				setTimeout(() => {
					uni.getBLEDeviceCharacteristics({
						deviceId: _this.ble_info.deviceId,
						serviceId: _this.serviceId,
						success(res) {
							console.log(res);
							res.characteristics.forEach((item) => {
								if (item.uuid.indexOf('8653000C') != -1) {
									const buffer = new ArrayBuffer(2)
									const dataView = new DataView(buffer)
									dataView.setUint8(0,0)
									dataView.setUint8(0,1)
									for(var i= 0;i<jsdjVal.length;i++){
										dataView.setUint8(1,jsdjVal[i])
									}
									uni.writeBLECharacteristicValue({
										deviceId: _this.ble_info.deviceId,
										serviceId: _this.serviceId,
										characteristicId: item.uuid,
										value: buffer,
										success(res) {
											var toast_info = {
												'title': '设置成功',
												'type': 'success',
											}
											_this.showToast(toast_info)
										},
										fail(res) {
											var toast_info = {
												'title': '设备写入数据失败,请检查蓝牙连接',
												'type': 'warning',
											}
											_this.showToast(toast_info)
										}
									})
								}
							})
						},
						fail(res) {
							var toast_info = {
								'title': '写入失败',
								'type': 'warning',
							}
							_this.showToast(toast_info)
							console.log(res);
						}
					})
				}, 1000)
			},
			//下发1-生物电势AFE通道数
			swdsTDs1(){
				var _this = this
				//获取频率转换成毫秒
				if(uni.getStorageSync('value9')!='' && uni.getStorageSync('value9')!=null){
					var jsval = uni.getStorageSync('value9')/12.5
				}else{
					var jsval = 1
				}
				//转16进制
				var jsdjVal = jsval.toString(16)
				//延时写入
				setTimeout(() => {
					uni.getBLEDeviceCharacteristics({
						deviceId: _this.ble_info.deviceId,
						serviceId: _this.serviceId,
						success(res) {
							console.log(res);
							res.characteristics.forEach((item) => {
								if (item.uuid.indexOf('8653000C') != -1) {
									const buffer = new ArrayBuffer(2)
									const dataView = new DataView(buffer)
									dataView.setUint8(0,0)
									dataView.setUint8(0,1)
									for(var i= 0;i<jsdjVal.length;i++){
										dataView.setUint8(1,jsdjVal[i])
									}
									uni.writeBLECharacteristicValue({
										deviceId: _this.ble_info.deviceId,
										serviceId: _this.serviceId,
										characteristicId: item.uuid,
										value: buffer,
										success(res) {
											var toast_info = {
												'title': '设置成功',
												'type': 'success',
											}
											_this.showToast(toast_info)
										},
										fail(res) {
											var toast_info = {
												'title': '设备写入数据失败,请检查蓝牙连接',
												'type': 'warning',
											}
											_this.showToast(toast_info)
										}
									})
								}
							})
						},
						fail(res) {
							var toast_info = {
								'title': '写入失败',
								'type': 'warning',
							}
							_this.showToast(toast_info)
							console.log(res);
						}
					})
				}, 1000)
			},
			//下发1-生物电势频率
			swdsVals1(){
				var _this = this
				//获取频率转换成毫秒
				if(uni.getStorageSync('value10')!='' && uni.getStorageSync('value10')!=null){
					var jsval = 1/uni.getStorageSync('value10')*25*1000
				}else{
					var jsval = 1/25*1000
				}
				//转16进制
				var jsdjVal = jsval.toString(16)
				//延时写入
				setTimeout(() => {
					uni.getBLEDeviceCharacteristics({
						deviceId: _this.ble_info.deviceId,
						serviceId: _this.serviceId,
						success(res) {
							console.log(res);
							res.characteristics.forEach((item) => {
								if (item.uuid.indexOf('8653000C') != -1) {
									const buffer = new ArrayBuffer(2)
									const dataView = new DataView(buffer)
									dataView.setUint8(0,0)
									dataView.setUint8(0,1)
									for(var i= 0;i<jsdjVal.length;i++){
										dataView.setUint8(1,jsdjVal[i])
									}
									uni.writeBLECharacteristicValue({
										deviceId: _this.ble_info.deviceId,
										serviceId: _this.serviceId,
										characteristicId: item.uuid,
										value: buffer,
										success(res) {
											var toast_info = {
												'title': '设置成功',
												'type': 'success',
											}
											_this.showToast(toast_info)
										},
										fail(res) {
											var toast_info = {
												'title': '设备写入数据失败,请检查蓝牙连接',
												'type': 'warning',
											}
											_this.showToast(toast_info)
										}
									})
								}
							})
						},
						fail(res) {
							var toast_info = {
								'title': '写入失败',
								'type': 'warning',
							}
							_this.showToast(toast_info)
							console.log(res);
						}
					})
				}, 1000)
			},
			//下发2-生物电势AFE通道数
			swdsTDs2(){
				var _this = this
				//获取频率转换成毫秒
				if(uni.getStorageSync('value11')!='' && uni.getStorageSync('value11')!=null){
					var jsval = uni.getStorageSync('value11')/12.5
				}else{
					var jsval = 1
				}
				//转16进制
				var jsdjVal = jsval.toString(16)
				//延时写入
				setTimeout(() => {
					uni.getBLEDeviceCharacteristics({
						deviceId: _this.ble_info.deviceId,
						serviceId: _this.serviceId,
						success(res) {
							console.log(res);
							res.characteristics.forEach((item) => {
								if (item.uuid.indexOf('8653000C') != -1) {
									const buffer = new ArrayBuffer(2)
									const dataView = new DataView(buffer)
									dataView.setUint8(0,0)
									dataView.setUint8(0,1)
									for(var i= 0;i<jsdjVal.length;i++){
										dataView.setUint8(1,jsdjVal[i])
									}
									uni.writeBLECharacteristicValue({
										deviceId: _this.ble_info.deviceId,
										serviceId: _this.serviceId,
										characteristicId: item.uuid,
										value: buffer,
										success(res) {
											var toast_info = {
												'title': '设置成功',
												'type': 'success',
											}
											_this.showToast(toast_info)
										},
										fail(res) {
											var toast_info = {
												'title': '设备写入数据失败,请检查蓝牙连接',
												'type': 'warning',
											}
											_this.showToast(toast_info)
										}
									})
								}
							})
						},
						fail(res) {
							var toast_info = {
								'title': '写入失败',
								'type': 'warning',
							}
							_this.showToast(toast_info)
							console.log(res);
						}
					})
				}, 1000)
			},
			//下发2-生物电势频率
			swdsVals2(){
				var _this = this
				//获取频率转换成毫秒
				if(uni.getStorageSync('value12')!='' && uni.getStorageSync('value12')!=null){
					var jsval = 1/uni.getStorageSync('value12')*25*1000
				}else{
					var jsval = 1/25*1000
				}
				//转16进制
				var jsdjVal = jsval.toString(16)
				//延时写入
				setTimeout(() => {
					uni.getBLEDeviceCharacteristics({
						deviceId: _this.ble_info.deviceId,
						serviceId: _this.serviceId,
						success(res) {
							console.log(res);
							res.characteristics.forEach((item) => {
								if (item.uuid.indexOf('8653000C') != -1) {
									const buffer = new ArrayBuffer(2)
									const dataView = new DataView(buffer)
									dataView.setUint8(0,0)
									dataView.setUint8(0,1)
									for(var i= 0;i<jsdjVal.length;i++){
										dataView.setUint8(1,jsdjVal[i])
									}
									uni.writeBLECharacteristicValue({
										deviceId: _this.ble_info.deviceId,
										serviceId: _this.serviceId,
										characteristicId: item.uuid,
										value: buffer,
										success(res) {
											var toast_info = {
												'title': '设置成功',
												'type': 'success',
											}
											_this.showToast(toast_info)
										},
										fail(res) {
											var toast_info = {
												'title': '设备写入数据失败,请检查蓝牙连接',
												'type': 'warning',
											}
											_this.showToast(toast_info)
										}
									})
								}
							})
						},
						fail(res) {
							var toast_info = {
								'title': '写入失败',
								'type': 'warning',
							}
							_this.showToast(toast_info)
							console.log(res);
						}
					})
				}, 1000)
			},
			toData(){
				var _this = this
				
				//监听蓝牙是否可用
				uni.openBluetoothAdapter({
				  success(res) {
					  _this.jsdjVals()
					  _this.tlyVals()
					  _this.cljVals()
					  _this.gxcgqVals()
					  _this.swdsTDs1()
					  _this.swdsVals1()
					  _this.swdsTDs2()
					  _this.swdsVals2()
				    uni.switchTab({
				    	url:'data'
				    })
				  },
				  fail() {
				  	this.uToast_info = {
						title:'请打开蓝牙'
					}
					_this.showToast(this.uToast_info)
				  }
				})
				
				
			}
		},
		onShow() {
			var _this = this
			var ble_link = uni.getStorageSync('ble_link')
			_this.ble_info = uni.getStorageSync('link_ble_info')
			// uni.getStorage({
			// 	key: 'link_ble_info',
			// 	success: function(res) {
			// 		console.log(res.data);
			// 		_this.ble_info = res.data
			// 		console.log(_this.ble_info);
			// 	}
			// })
			console.log(ble_link);
			console.log(_this.ble_info);
			//判断蓝牙是否连接
			if (ble_link.connected == true) {
				//蓝牙已经连接,无需重复连接
				uni.openBluetoothAdapter({
					complete(res) {
						console.log(res);
						//获取所有服务
						uni.getBLEDeviceServices({
							deviceId: _this.ble_info.deviceId,
							success(res) {
								_this.ble_services = res.services
								res.services.forEach((item) => {
									console.log("进入循环" + item.uuid);
									if (item.uuid.indexOf("8653000A") != -1) {
										_this.serviceId = item.uuid;
			
									}
								})
			
							},
							fail(res) {
								console.log(res);
							}
						})
					}
				}, 1000)
			}
			
			
			this.linked = uni.getStorageSync('link_ble_info').name
			//传感器配置回显
			if(uni.getStorageSync('checked1')!='' && uni.getStorageSync('checked1')!=null){
				this.checked1 = uni.getStorageSync('checked1')
			}
			if(uni.getStorageSync('checked2')!='' && uni.getStorageSync('checked2')!=null){
				this.checked2 = uni.getStorageSync('checked2')
			}
			if(uni.getStorageSync('checked3')!='' && uni.getStorageSync('checked3')!=null){
				this.checked3 = uni.getStorageSync('checked3')
			}
			if(uni.getStorageSync('checked4')!='' && uni.getStorageSync('checked4')!=null){
				this.checked4 = uni.getStorageSync('checked4')
			}
			if(uni.getStorageSync('checked5')!='' && uni.getStorageSync('checked5')!=null){
				this.checked5 = uni.getStorageSync('checked5')
			}
			if(uni.getStorageSync('checked6')!='' && uni.getStorageSync('checked6')!=null){
				this.checked6 = uni.getStorageSync('checked6')
			}
			if(uni.getStorageSync('checked7')!='' && uni.getStorageSync('checked7')!=null){
				this.checked7 = uni.getStorageSync('checked7')
			}
			if(uni.getStorageSync('checked8')!='' && uni.getStorageSync('checked8')!=null){
				this.checked8 = uni.getStorageSync('checked8')
			}
			if(uni.getStorageSync('checked9')!='' && uni.getStorageSync('checked9')!=null){
				this.checked9 = uni.getStorageSync('checked9')
			}
			if(uni.getStorageSync('checked10')!='' && uni.getStorageSync('checked10')!=null){
				this.checked10 = uni.getStorageSync('checked10')
			}
			
			this.value1 = uni.getStorageSync('value1')
			this.value2 = uni.getStorageSync('value2')
			this.value3 = uni.getStorageSync('value3')
			this.value4 = uni.getStorageSync('value4')
			this.value9 = uni.getStorageSync('value9')
			this.value10 = uni.getStorageSync('value10')
			this.value11 = uni.getStorageSync('value11')
			this.value12 = uni.getStorageSync('value12')
		},
		watch:{
			//监听加速度计开关
			checked1(val){
				uni.setStorageSync('checked1',val)
			},
			//监听陀螺仪开关
			checked2(val){
				uni.setStorageSync('checked2',val)
			},
			//监听加磁力计开关
			checked3(val){
				uni.setStorageSync('checked3',val)
			},
			//监听光学传感器开关
			checked4(val){
				uni.setStorageSync('checked4',val)
			},
			//光学传感器选择互斥
			checked5(val){
				var _this = this
				uni.setStorageSync('checked5',val)
				//灯光开关
				if(val){
					// var jsval = 01
					var jsdjVal = '01'.toString(16)
					setTimeout(() => {
						uni.getBLEDeviceCharacteristics({
							deviceId: _this.ble_info.deviceId,
							serviceId: _this.serviceId,
							success(res) {
								console.log(res);
								res.characteristics.forEach((item) => {
									if (item.uuid.indexOf('8653000C') != -1) {
										const buffer = new ArrayBuffer(2)
										const dataView = new DataView(buffer)
										dataView.setUint8(0,0)
										dataView.setUint8(0,1)
										for(var i= 0;i<jsdjVal.length;i++){
											dataView.setUint8(1,jsdjVal[i])
										}
										uni.writeBLECharacteristicValue({
											deviceId: _this.ble_info.deviceId,
											serviceId: _this.serviceId,
											characteristicId: item.uuid,
											value: buffer,
											success(res) {
												var toast_info = {
													'title': '设置成功',
													'type': 'success',
												}
												_this.showToast(toast_info)
												console.log(res);
											},
											fail(res) {
												var toast_info = {
													'title': '设备写入数据失败,请检查蓝牙连接',
													'type': 'warning',
												}
												_this.showToast(toast_info)
												console.log(res);
											}
										})
									}
								})
							},
							fail(res) {
								var toast_info = {
									'title': '写入失败',
									'type': 'warning',
								}
								_this.showToast(toast_info)
								console.log(res);
							}
						})
					}, 1000)
				}
				if(!val){
					// var jsval = 02
					var jsdjVal = '02'.toString(16)
					setTimeout(() => {
						uni.getBLEDeviceCharacteristics({
							deviceId: _this.ble_info.deviceId,
							serviceId: _this.serviceId,
							success(res) {
								console.log(res);
								res.characteristics.forEach((item) => {
									if (item.uuid.indexOf('8653000C') != -1) {
										const buffer = new ArrayBuffer(2)
										const dataView = new DataView(buffer)
										dataView.setUint8(0,0)
										dataView.setUint8(0,1)
										for(var i= 0;i<jsdjVal.length;i++){
											dataView.setUint8(1,jsdjVal[i])
										}
										uni.writeBLECharacteristicValue({
											deviceId: _this.ble_info.deviceId,
											serviceId: _this.serviceId,
											characteristicId: item.uuid,
											value: buffer,
											success(res) {
												var toast_info = {
													'title': '设置成功',
													'type': 'success',
												}
												_this.showToast(toast_info)
												console.log(res);
											},
											fail(res) {
												var toast_info = {
													'title': '设备写入数据失败,请检查蓝牙连接',
													'type': 'warning',
												}
												_this.showToast(toast_info)
												console.log(res);
											}
										})
									}
								})
							},
							fail(res) {
								var toast_info = {
									'title': '写入失败',
									'type': 'warning',
								}
								_this.showToast(toast_info)
								console.log(res);
							}
						})
					}, 1000)
				}
				// if(val){
				// 	this.checked6 = false
				// 	this.checked7 = false
				// 	this.checked8 = false
				// }
			},
			checked6(val){
				var _this = this
				uni.setStorageSync('checked6',val)
				//灯光开关
				if(val){
					var jsdjVal = '01'.toString(16)
					setTimeout(() => {
						uni.getBLEDeviceCharacteristics({
							deviceId: _this.ble_info.deviceId,
							serviceId: _this.serviceId,
							success(res) {
								console.log(res);
								res.characteristics.forEach((item) => {
									if (item.uuid.indexOf('8653000C') != -1) {
										const buffer = new ArrayBuffer(2)
										const dataView = new DataView(buffer)
										dataView.setUint8(0,0)
										dataView.setUint8(0,1)
										for(var i= 0;i<jsdjVal.length;i++){
											dataView.setUint8(1,jsdjVal[i])
										}
										uni.writeBLECharacteristicValue({
											deviceId: _this.ble_info.deviceId,
											serviceId: _this.serviceId,
											characteristicId: item.uuid,
											value: buffer,
											success(res) {
												var toast_info = {
													'title': '设置成功',
													'type': 'success',
												}
												_this.showToast(toast_info)
											},
											fail(res) {
												var toast_info = {
													'title': '设备写入数据失败,请检查蓝牙连接',
													'type': 'warning',
												}
												_this.showToast(toast_info)
											}
										})
									}
								})
							},
							fail(res) {
								var toast_info = {
									'title': '写入失败',
									'type': 'warning',
								}
								_this.showToast(toast_info)
								console.log(res);
							}
						})
					}, 1000)
				}else{
					var jsdjVal = '02'.toString(16)
					setTimeout(() => {
						uni.getBLEDeviceCharacteristics({
							deviceId: _this.ble_info.deviceId,
							serviceId: _this.serviceId,
							success(res) {
								console.log(res);
								res.characteristics.forEach((item) => {
									if (item.uuid.indexOf('8653000C') != -1) {
										const buffer = new ArrayBuffer(2)
										const dataView = new DataView(buffer)
										dataView.setUint8(0,0)
										dataView.setUint8(0,1)
										for(var i= 0;i<jsdjVal.length;i++){
											dataView.setUint8(1,jsdjVal[i])
										}
										uni.writeBLECharacteristicValue({
											deviceId: _this.ble_info.deviceId,
											serviceId: _this.serviceId,
											characteristicId: item.uuid,
											value: buffer,
											success(res) {
												var toast_info = {
													'title': '设置成功',
													'type': 'success',
												}
												_this.showToast(toast_info)
											},
											fail(res) {
												var toast_info = {
													'title': '设备写入数据失败,请检查蓝牙连接',
													'type': 'warning',
												}
												_this.showToast(toast_info)
											}
										})
									}
								})
							},
							fail(res) {
								var toast_info = {
									'title': '写入失败',
									'type': 'warning',
								}
								_this.showToast(toast_info)
								console.log(res);
							}
						})
					}, 1000)
				}
				// if(val){
				// 	this.checked5 = false
				// 	this.checked7 = false
				// 	this.checked8 = false
				// }
			},
			checked7(val){
				var _this = this
				uni.setStorageSync('checked7',val)
				//灯光开关
				if(val){
					var jsdjVal = '01'.toString(16)
					setTimeout(() => {
						uni.getBLEDeviceCharacteristics({
							deviceId: _this.ble_info.deviceId,
							serviceId: _this.serviceId,
							success(res) {
								console.log(res);
								res.characteristics.forEach((item) => {
									if (item.uuid.indexOf('8653000C') != -1) {
										const buffer = new ArrayBuffer(2)
										const dataView = new DataView(buffer)
										dataView.setUint8(0,0)
										dataView.setUint8(0,1)
										for(var i= 0;i<jsdjVal.length;i++){
											dataView.setUint8(1,jsdjVal[i])
										}
										uni.writeBLECharacteristicValue({
											deviceId: _this.ble_info.deviceId,
											serviceId: _this.serviceId,
											characteristicId: item.uuid,
											value: buffer,
											success(res) {
												var toast_info = {
													'title': '设置成功',
													'type': 'success',
												}
												_this.showToast(toast_info)
											},
											fail(res) {
												var toast_info = {
													'title': '设备写入数据失败,请检查蓝牙连接',
													'type': 'warning',
												}
												_this.showToast(toast_info)
											}
										})
									}
								})
							},
							fail(res) {
								var toast_info = {
									'title': '写入失败',
									'type': 'warning',
								}
								_this.showToast(toast_info)
								console.log(res);
							}
						})
					}, 1000)
				}else{
					var jsdjVal = '02'.toString(16)
					setTimeout(() => {
						uni.getBLEDeviceCharacteristics({
							deviceId: _this.ble_info.deviceId,
							serviceId: _this.serviceId,
							success(res) {
								console.log(res);
								res.characteristics.forEach((item) => {
									if (item.uuid.indexOf('8653000C') != -1) {
										const buffer = new ArrayBuffer(2)
										const dataView = new DataView(buffer)
										dataView.setUint8(0,0)
										dataView.setUint8(0,1)
										for(var i= 0;i<jsdjVal.length;i++){
											dataView.setUint8(1,jsdjVal[i])
										}
										uni.writeBLECharacteristicValue({
											deviceId: _this.ble_info.deviceId,
											serviceId: _this.serviceId,
											characteristicId: item.uuid,
											value: buffer,
											success(res) {
												var toast_info = {
													'title': '设置成功',
													'type': 'success',
												}
												_this.showToast(toast_info)
											},
											fail(res) {
												var toast_info = {
													'title': '设备写入数据失败,请检查蓝牙连接',
													'type': 'warning',
												}
												_this.showToast(toast_info)
											}
										})
									}
								})
							},
							fail(res) {
								var toast_info = {
									'title': '写入失败',
									'type': 'warning',
								}
								_this.showToast(toast_info)
								console.log(res);
							}
						})
					}, 1000)
				}
				// if(val){
				// 	this.checked6 = false
				// 	this.checked5 = false
				// 	this.checked8 = false
				// }
			},
			checked8(val){
				var _this = this
				uni.setStorageSync('checked8',val)
				//灯光开关
				if(val){
					var jsdjVal = '01'.toString(16)
					setTimeout(() => {
						uni.getBLEDeviceCharacteristics({
							deviceId: _this.ble_info.deviceId,
							serviceId: _this.serviceId,
							success(res) {
								console.log(res);
								res.characteristics.forEach((item) => {
									if (item.uuid.indexOf('8653000C') != -1) {
										const buffer = new ArrayBuffer(2)
										const dataView = new DataView(buffer)
										dataView.setUint8(0,0)
										dataView.setUint8(0,1)
										for(var i= 0;i<jsdjVal.length;i++){
											dataView.setUint8(1,jsdjVal[i])
										}
										uni.writeBLECharacteristicValue({
											deviceId: _this.ble_info.deviceId,
											serviceId: _this.serviceId,
											characteristicId: item.uuid,
											value: buffer,
											success(res) {
												var toast_info = {
													'title': '设置成功',
													'type': 'success',
												}
												_this.showToast(toast_info)
											},
											fail(res) {
												var toast_info = {
													'title': '设备写入数据失败,请检查蓝牙连接',
													'type': 'warning',
												}
												_this.showToast(toast_info)
											}
										})
									}
								})
							},
							fail(res) {
								var toast_info = {
									'title': '写入失败',
									'type': 'warning',
								}
								_this.showToast(toast_info)
								console.log(res);
							}
						})
					}, 1000)
				}else{
					var jsdjVal = '02'.toString(16)
					setTimeout(() => {
						uni.getBLEDeviceCharacteristics({
							deviceId: _this.ble_info.deviceId,
							serviceId: _this.serviceId,
							success(res) {
								console.log(res);
								res.characteristics.forEach((item) => {
									if (item.uuid.indexOf('8653000C') != -1) {
										const buffer = new ArrayBuffer(2)
										const dataView = new DataView(buffer)
										dataView.setUint8(0,0)
										dataView.setUint8(0,1)
										for(var i= 0;i<jsdjVal.length;i++){
											dataView.setUint8(1,jsdjVal[i])
										}
										uni.writeBLECharacteristicValue({
											deviceId: _this.ble_info.deviceId,
											serviceId: _this.serviceId,
											characteristicId: item.uuid,
											value: buffer,
											success(res) {
												var toast_info = {
													'title': '设置成功',
													'type': 'success',
												}
												_this.showToast(toast_info)
											},
											fail(res) {
												var toast_info = {
													'title': '设备写入数据失败,请检查蓝牙连接',
													'type': 'warning',
												}
												_this.showToast(toast_info)
											}
										})
									}
								})
							},
							fail(res) {
								var toast_info = {
									'title': '写入失败',
									'type': 'warning',
								}
								_this.showToast(toast_info)
								console.log(res);
							}
						})
					}, 1000)
				}
				// if(val){
				// 	this.checked6 = false
				// 	this.checked7 = false
				// 	this.checked5 = false
				// }
			},
			//监听1-生物电势AFE开关
			checked9(val){
				uni.setStorageSync('checked9',val)
			},
			//监听2-生物电势AFE开关
			checked10(val){
				uni.setStorageSync('checked10',val)
			},
			//监听加速度计频率
			value1(val){
				uni.setStorageSync('value1',val)
			},
			//监听陀螺仪频率
			value2(val){
				uni.setStorageSync('value2',val)
			},
			//监听磁力计频率
			value3(val){
				uni.setStorageSync('value3',val)
			},
			//监听光学传感器频率
			value4(val){
				uni.setStorageSync('value4',val)
			},
			//监听1-生物电势AFE通道
			value9(val){
				uni.setStorageSync('value9',val)
			},
			//监听1-生物电势AFE采集频率
			value10(val){
				uni.setStorageSync('value10',val)
			},
			//监听2-生物电势AFE通道
			value11(val){
				uni.setStorageSync('value11',val)
			},
			//监听2-生物电势AFE采集频率
			value12(val){
				uni.setStorageSync('value12',val)
			},
		}
	}
</script>

<style lang="scss">
	.cell {
		margin-top: 20upx;
		display: flex;
		justify-content: space-around;
	}
	.sliders {
		margin-top: 20upx;
		display: flex;
		justify-content: space-around;
		.slider {
			margin-top: 20upx;
			width: 60%;
		}
	}
</style>
