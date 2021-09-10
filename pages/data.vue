<template>
	<view>
		<view class="data">
			<text class="text">数据保存</text>
			<u-input v-model="value" type="text" border placeholder='输入文件名' :disabled = 'disSave'/>
		</view>
		<view class="data">
			<text class="text1">定时</text>
			<u-input v-model="value1" type="number" border placeholder='时长' :disabled = 'disSave'/>
			<text class="text">S</text>
			<u-switch v-model="checked" :disabled = 'disSave'></u-switch>
		</view>
		<view class="data">
			<u-button type="success" size='medium' @click='starts' :disabled='disStart'>开始</u-button>
			<u-button type="success" size='medium' @click = 'suspend' :disabled='disSuspend'>暂停</u-button>
			<u-button type="success" size='medium' @click = 'ends' :disabled='disEnd'>结束</u-button>
		</view>
		<view class="data">
			<text class="text">传感器状态</text>
			<u-input v-model="value2" type="text" border disabled />
		</view>
		<view class="data">
			<text class="text">数据长度(S)</text>
			<u-input v-model="value3" type="text" border disabled />
		</view>
		<view class="data">
			<u-select v-model="show" @click="show = !show" :list="list" @confirm="confirm" ></u-select>
			<u-input v-model="value4" type="text" border disabled @click="show = !show" placeholder='输入传感器'/>
			<u-button type="success" size='medium' style='margin-left:20upx;margin-right: 20upx;' @click="startCJ">开始</u-button>
			<u-button  type="success" size='medium' >结束</u-button>
		</view>
		<view class="chart">
			<view class="charts-box">
			  <qiun-data-charts
			    type="demotype"
			    :chartData="chartData"
			    background="none"
				:animation="false"
			  />
			</view>
		</view>
		<view class="data" style="margin-top: 30upx;">
			<!-- <u-button type="success" size='medium' @click="closeBlue">关闭蓝牙连接</u-button> -->
			<u-button type="success" size='medium' @click="open">查看历史保存数据</u-button>
		</view>
		<view>
			<u-toast ref="uToast" />
		</view>
	</view>
</template>

<script>
	import doExport from '../common/exportExcel.js'
	export default {
		data() {
			return {
				dsSave:false,//定时保存
				disSave:false,
				value:'',
				value1:'',
				value2:'连接正常',
				value3:'0',
				value4:'',
				checked:false,
				show:false,
				disStart:false,
				disSuspend:true,
				disEnd:true,
				autoplay:false,
				setIntervalId:'',
				ble_info: '', //连接的蓝牙设备信息
				ble_services: '', //蓝牙设备服务信息
				ble_character: '', //蓝牙设备特征值
				blelink: {
					'deviceId': '',
					'connected': false
				}, //蓝牙设备连接状态
				ble_type: '未连接',
				bluShow: false, //模态框
				content: '设备自动连接失败,是否进入手动连接',
				
				data1:[],
				data2:[],
				data3:[],
				data4:[],
				data5:[],
				data:[
					[
						{content: 'X轴',}, 
						{content: 'Y轴',}, 
						{content: 'Z轴',},
					],
					[
						{content: ''}, 
						{content: ''}, 
						{content: ''},
						],
					
				],
				//加速度计数据
					jsdjData : [
						{
							name:'x轴',
							data:[]
						},
						{
							name:'y轴',
							data:[]
						},
						{
							name:'z轴',
							data:[]
						}
					],
				//导出加速度计数据
					jsdjData1 : [
						{
							date:[],
							name:'x轴',
							data:[]
						},
						{
							name:'y轴',
							data:[]
						},
						{
							name:'z轴',
							data:[]
						}
					],
					
				
				//陀螺仪数据
				tlyData : [
					{
						name:'x轴',
						data:[]
					},
					{
						name:'y轴',
						data:[]
					},
					{
						name:'z轴',
						data:[]
					}
				],
				//陀导出螺仪数据
				tlyData1 : [
					{
						date:[],
						name:'x轴',
						data:[]
					},
					{
						name:'y轴',
						data:[]
					},
					{
						name:'z轴',
						data:[]
					}
				],
				//磁力计数据
				cljData : [
					{
						name:'x轴',
						data:[]
					},
					{
						name:'y轴',
						data:[]
					},
					{
						name:'z轴',
						data:[]
					}
				],
				//导出磁力计数据
				cljData1 : [
					{
						date:[],
						name:'x轴',
						data:[]
					},
					{
						name:'y轴',
						data:[]
					},
					{
						name:'z轴',
						data:[]
					}
				],
				//血压心率数据
				xyxlData:[
					{
						name:'血压值',
						data:[]
					},
					{
						name:'心率值',
						data:[]
					},
				],
				//导出血压心率数据
				xyxlData1:[
					{
						date:[],
						name:'血压值',
						data:[]
					},
					{
						name:'心率值',
						data:[]
					},
				],
				//1-传感器AFE数据
				AFEData1:[
					{
						name:'通道1',
						data:[]
					},
					{
						name:'通道2',
						data:[]
					},
					{
						name:'通道3',
						data:[]
					},
					{
						name:'通道4',
						data:[]
					},
					{
						name:'通道5',
						data:[]
					},
					{
						name:'通道6',
						data:[]
					},
					{
						name:'通道7',
						data:[]
					},
					{
						name:'通道8',
						data:[]
					},
				],
				//导出1-传感器AFE数据
				AFEData11:[
					{
						date:[],
						name:'通道1',
						data:[]
					},
					{
						name:'通道2',
						data:[]
					},
					{
						name:'通道3',
						data:[]
					},
					{
						name:'通道4',
						data:[]
					},
					{
						name:'通道5',
						data:[]
					},
					{
						name:'通道6',
						data:[]
					},
					{
						name:'通道7',
						data:[]
					},
					{
						name:'通道8',
						data:[]
					},
				],
				//2-传感器AFE数据
				AFEData2:[
					{
						name:'通道1',
						data:[]
					},
					{
						name:'通道2',
						data:[]
					},
					{
						name:'通道3',
						data:[]
					},
					{
						name:'通道4',
						data:[]
					},
					{
						name:'通道5',
						data:[]
					},
					{
						name:'通道6',
						data:[]
					},
					{
						name:'通道7',
						data:[]
					},
					{
						name:'通道8',
						data:[]
					},
				],
				//导出2-传感器AFE数据
				AFEData22:[
					{
						date:[],
						name:'通道1',
						data:[]
					},
					{
						name:'通道2',
						data:[]
					},
					{
						name:'通道3',
						data:[]
					},
					{
						name:'通道4',
						data:[]
					},
					{
						name:'通道5',
						data:[]
					},
					{
						name:'通道6',
						data:[]
					},
					{
						name:'通道7',
						data:[]
					},
					{
						name:'通道8',
						data:[]
					},
				],
				list: [
						{
							value: '1',
							label: '加速度计'
						},
						{
							value: '2',
							label: '陀螺仪'
						},
						{
							value: '3',
							label: '磁力计'
						},
						{
							value: '4',
							label: '光学传感器'
						},
						{
							value: '5',
							label: '1-生物电势AFE'
						},
						{
							value: '6',
							label: '2-生物电势AFE'
						}
					],
					chartData:{
						"categories": [
							"1",
							"2",
							"3",
							"4",
							"5",
							"6",
							"7",
							"8",
							"9",
							"10",
						],
						"series": [
							{
								name:'通道1',
								data:[]
							},
							{
								name:'通道2',
								data:[]
							},
							{
								name:'通道3',
								data:[]
							},
						]
},
			}
		},
		onShow() {
			
			
				// for(var j=0;j<this.jsdjData_x.length;j++){
				// 	this.data.push(this.jsdjData_x[j])
				// }
			
			//json对象转二维数据
			
			// for(var i=0;i< this.jsdjData_x.length;i++){
				
			// 	console.log(this.jsdjData_x[i]);
				
			// 	this.data[i][0].content = this.jsdjData_x[i]
				
			// }
			
			uni.openBluetoothAdapter({
			  success(res) {
			    console.log(res)
			  }
			})
			
			var _this = this
			// var deviceId ='DD:9C:3A:ED:78:3F'
			// var serviceId = '00001800-0000-1000-8000-00805F9B34FB'
			// var characteristicId = '00002A00-0000-1000-8000-00805F9B34FB'
			//数据上报
			//蓝牙连接操作
			//判断蓝牙有无连接设备
			//读取缓存数据
			uni.getStorage({
				key: 'link_ble_info',
				complete: function(res) {
					console.log(res);
					//判断是否是初次连接
					if (res.data == null || res.data == '') {
						_this.bluShow = true
					} else {
						_this.ble_info = res.data
						const ble_link = uni.getStorageSync('ble_link')
						console.log('蓝牙连接信息' + ble_link.connected);
						if (ble_link.connected == true) {
							_this.value2 = '连接正常'
							_this.ble_type = '已连接'
							//蓝牙已经连接,无需重复连接
							uni.openBluetoothAdapter({
								success() {
									//获取所有服务
									uni.getBLEDeviceServices({
										deviceId: _this
											.ble_info
											.deviceId,
										success(res) {
											
											_this.ble_services = res.services
											res.services.forEach((item) => {
												console.log("进入循环" + item.uuid);
												if (item.uuid.indexOf("8653000A") != -
													1) {
													_this.serviceId = item.uuid;
													//进入特征
													_this.getBLEDeviceCharacteristics()
												}
											})
			
										}
									})
								}
							}, 1000)
			
							//蓝牙未连接,重新连接
						} else {
							_this.value2 = '连接异常'
							uni.openBluetoothAdapter({
								success() {
									uni.createBLEConnection({
										deviceId: _this.ble_info.deviceId,
										timeout: 2000,
										fail(res) {
											console.log(res);
											_this.bluShow = true
			
										},
										success() {
											_this.value2 = '连接正常'
											var toast_info = {
												'title': '设备自动连接成功',
												'type': 'success',
											}
											_this.showToast(toast_info)
			
											//获取所有服务
											setTimeout(() => {
												uni.getBLEDeviceServices({
													deviceId: _this.ble_info
														.deviceId,
													fail(res) {
														console.log(res);
													},
													success(res) {
														console.log(res);
														_this.ble_services =
															res.services
														res.services.forEach((
															item) => {
															console
																.log(
																	"进入循环1" +
																	item
																	.uuid
																	);
															if (item
																.uuid
																.indexOf(
																	"8653000A"
																	) !=
																-1) {
																_this
																	.serviceId =
																	item
																	.uuid;
																//进入特征
																_this
																	.getBLEDeviceCharacteristics()
															}
														})
			
													}
												})
											}, 3000)
			
										}
									})
								}
							})
			
						}
						//}
			
					}
				}
			})
			
		},
		methods: {
			je(data) {
			    var arr = []
			    for (var i in data) {
			        arr[i] = [];
			        for (var j in data[i]) {
			            arr[i].push(data[i][j])
			        }
			    }
			    return arr
			
			},
			//获取蓝牙特征值
			getBLEDeviceCharacteristics() {
				//16进制转成10进制
				function hex2int(hex) {
					var len = hex.length,
						a = new Array(len),
						code;
					for (var i = 0; i < len; i++) {
						code = hex.charCodeAt(i);
						if (48 <= code && code < 58) {
							code -= 48;
						} else {
							code = (code & 0xdf) - 65 + 10;
						}
						a[i] = code;
					}
			
					return a.reduce(function(acc, c) {
						acc = 16 * acc + c;
						return acc;
					}, 0);
				}
			
				//转码16进制
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
				console.log("进入获取蓝牙特征值");
				//获取蓝牙特征值
				setTimeout(() => {
					
					uni.getBLEDeviceCharacteristics({
						deviceId: _this.ble_info.deviceId,
						serviceId: _this.serviceId,
						success(res) {
						
							_this.ble_character = res.characteristics
							res.characteristics.forEach((item) => {
								
								console.log('uuid' + item.uuid);
								if (item.uuid.indexOf('8653000B') != -1) {
								
									_this.notifyBLECharacteristicValueChange(item.uuid)
									uni.onBLECharacteristicValueChange(function(res) {
										console.log(
											`characteristic ${res.characteristicId} has changed, now is ${res.value}`
											)
										console.log(ab2hex(res.value))
										//数据解析
										var linData = ab2hex(res.value)
										//获取当前时间
										let date = new Date();
										let year = date.getFullYear();
										let month = date.getMonth() + 1;
										let day = date.getDate();
										let hour = date.getHours();
										let minute = date.getMinutes();
										let second = date.getSeconds();
										if(month < 10){
											month = '0' + month;
										}
										if(day < 10){
											day = '0' + day;
										}
										if(hour < 10){
											hour = '0' + hour;
										}
										if(minute < 10){
											minute = '0' + minute;
										}
										if(second < 10){
											second = '0' + second;
										}
										var time = year+'年' + month+'月' + day+'日' + hour+'时' + minute+'分' + second+'秒'
										if(linData.substring(0,4)=='aaaa'){
											//加速度计数据
											_this.jsdjData[0].data.push(hex2int(linData.substring(4,8)))
											_this.jsdjData[1].data.push(hex2int(linData.substring(8,12)))
											_this.jsdjData[2].data.push(hex2int(linData.substring(12,16)))
											//导出加速度计数据
											if(_this.dsSave){
												_this.jsdjData1[0].data.push(hex2int(linData.substring(4,8)))
												_this.jsdjData1[1].data.push(hex2int(linData.substring(8,12)))
												_this.jsdjData1[2].data.push(hex2int(linData.substring(12,16)))
												_this.jsdjData1[0].date.push(time)
											}
											
											
											
											//陀螺仪数据
											_this.tlyData[0].data.push(hex2int(linData.substring(16,20)))
											_this.tlyData[1].data.push(hex2int(linData.substring(20,24)))
											_this.tlyData[2].data.push(hex2int(linData.substring(24,28)))
											//导出陀螺仪数据
											if(_this.dsSave){
												_this.tlyData1[0].data.push(hex2int(linData.substring(16,20)))
												_this.tlyData1[1].data.push(hex2int(linData.substring(20,24)))
												_this.tlyData1[2].data.push(hex2int(linData.substring(24,28)))
												_this.tlyData1[0].date.push(time)
											}
											//磁力计数据
											_this.cljData[0].data.push(hex2int(linData.substring(28,32)))
											_this.cljData[1].data.push(hex2int(linData.substring(32,36)))
											_this.cljData[2].data.push(hex2int(linData.substring(36,40)))
											//导出磁力计数据
											if(_this.dsSave){
												_this.cljData1[0].data.push(hex2int(linData.substring(28,32)))
												_this.cljData1[1].data.push(hex2int(linData.substring(32,36)))
												_this.cljData1[2].data.push(hex2int(linData.substring(36,40)))
												_this.cljData1[0].date.push(time)
											}
										}
										
										if(linData.substring(0,4)=='bbbb'){
											//血压心率值
											_this.xyxlData[0].data.push(hex2int(linData.substring(4,8)))
											_this.xyxlData[1].data.push(hex2int(linData.substring(8,12)))
											//导出血压心率值
											if(_this.dsSave){
												_this.xyxlData1[0].data.push(hex2int(linData.substring(4,8)))
												_this.xyxlData1[1].data.push(hex2int(linData.substring(8,12)))
												_this.xyxlData1[0].date.push(time)
											}
										}
										
										if(linData.substring(0,6)=='cccc01'){
											//1-传感器AFE
											_this.AFEData1[0].data.push(hex2int(linData.substring(6,14)))
											_this.AFEData1[1].data.push(hex2int(linData.substring(14,22)))
											_this.AFEData1[2].data.push(hex2int(linData.substring(22,30)))
											_this.AFEData1[3].data.push(hex2int(linData.substring(30,38)))
											//导出1-传感器AFE
											if(_this.dsSave){
												_this.AFEData11[0].data.push(hex2int(linData.substring(6,14)))
												_this.AFEData11[1].data.push(hex2int(linData.substring(14,22)))
												_this.AFEData11[2].data.push(hex2int(linData.substring(22,30)))
												_this.AFEData11[3].data.push(hex2int(linData.substring(30,38)))
												_this.AFEData11[0].date.push(time)
											}
										}
										
										if(linData.substring(0,6)=='cccc02'){
											//1-传感器AFE
											_this.AFEData1[4].data.push(hex2int(linData.substring(6,14)))
											_this.AFEData1[5].data.push(hex2int(linData.substring(14,22)))
											_this.AFEData1[6].data.push(hex2int(linData.substring(6,14)))
											_this.AFEData1[7].data.push(hex2int(linData.substring(14,22)))
											//导出1-传感器AFE
											if(_this.dsSave){
												_this.AFEData11[4].data.push(hex2int(linData.substring(6,14)))
												_this.AFEData11[5].data.push(hex2int(linData.substring(14,22)))
												_this.AFEData11[6].data.push(hex2int(linData.substring(6,14)))
												_this.AFEData11[7].data.push(hex2int(linData.substring(14,22)))
											}
										}
										
										if(linData.substring(0,6)=='dddd01'){
											//2-传感器AFE
											_this.AFEData2[0].data.push(hex2int(linData.substring(6,14)))
											_this.AFEData2[1].data.push(hex2int(linData.substring(14,22)))
											_this.AFEData2[2].data.push(hex2int(linData.substring(22,30)))
											_this.AFEData2[3].data.push(hex2int(linData.substring(30,38)))
											//导出2-传感器AFE
											if(_this.dsSave){
												_this.AFEData22[0].data.push(hex2int(linData.substring(6,14)))
												_this.AFEData22[1].data.push(hex2int(linData.substring(14,22)))
												_this.AFEData22[2].data.push(hex2int(linData.substring(22,30)))
												_this.AFEData22[3].data.push(hex2int(linData.substring(30,38)))
												_this.AFEData22[0].date.push(time)
											}
										}
										
										if(linData.substring(0,6)=='dddd02'){
											//2-传感器AFE
											_this.AFEData2[4].data.push(hex2int(linData.substring(6,14)))
											_this.AFEData2[5].data.push(hex2int(linData.substring(14,22)))
											_this.AFEData2[6].data.push(hex2int(linData.substring(6,14)))
											_this.AFEData2[7].data.push(hex2int(linData.substring(14,22)))
											//导出2-传感器AFE
											if(_this.dsSave){
												_this.AFEData22[4].data.push(hex2int(linData.substring(6,14)))
												_this.AFEData22[5].data.push(hex2int(linData.substring(14,22)))
												_this.AFEData22[6].data.push(hex2int(linData.substring(6,14)))
												_this.AFEData22[7].data.push(hex2int(linData.substring(14,22)))
											}
										}
										
										
										
									})
			
								}
								
							})
							
						},
						fail(res) {
							_this.value2 = '连接异常'
							console.log(res);
						}
					})
			
			
				}, 1000)
			
			},
			
			//数据异步上传
			notifyBLECharacteristicValueChange(characteristicId) {
			
				var _this = this
				uni.notifyBLECharacteristicValueChange({
					state: true, //启用notify功能
					deviceId: _this.ble_info.deviceId,
					serviceId: _this.serviceId,
					characteristicId: characteristicId,
					success(res) {
			
					},
					fail(res) {
						console.log(res);
					}
				})
			},
			
			//下拉列表查询返回
			confirm(e){
				this.value4 = e[0].label
			},
			exports(){
				this.data = []
				this.data1 = []
				this.data2 = []
				this.data3 = []
				this.data4 = []
				this.data5 = []
				//导出加速度计数据
				this.data.push([{'content':'X轴'},{'content':'Y轴'},{'content':'Z轴'},{'content':'时间'}])
					for(var i = 0;i<this.jsdjData1[0].data.length;i++){
						var x = this.jsdjData1[0].data[i]
						var y = this.jsdjData1[1].data[i]
						var z = this.jsdjData1[2].data[i]
						var time = this.jsdjData1[0].date[i]
						var SaveData = [{'content':x},{'content':y},{'content':z},{'content':time}]
						this.data.push(SaveData)
					}
				doExport(this.value+'加速度计',this.data,this.callback)
				
				//导出陀螺仪数据
				this.data1.push([{'content':'X轴'},{'content':'Y轴'},{'content':'Z轴'},{'content':'时间'}])
					for(var i = 0;i<this.tlyData1[0].data.length;i++){
						var x = this.tlyData1[0].data[i]
						var y = this.tlyData1[1].data[i]
						var z = this.tlyData1[2].data[i]
						var time = this.tlyData1[0].date[i]
						var SaveData = [{'content':x},{'content':y},{'content':z},{'content':time}]
						this.data1.push(SaveData)
					}
				doExport(this.value+'陀螺仪',this.data1,this.callback)
				
				//导出磁力计数据
				this.data2.push([{'content':'X轴'},{'content':'Y轴'},{'content':'Z轴'},{'content':'时间'}])
					for(var i = 0;i<this.cljData1[0].data.length;i++){
						var x = this.cljData1[0].data[i]
						var y = this.cljData1[1].data[i]
						var z = this.cljData1[2].data[i]
						var time = this.cljData1[0].date[i]
						var SaveData = [{'content':x},{'content':y},{'content':z},{'content':time}]
						this.data2.push(SaveData)
					}
				doExport(this.value+'磁力计',this.data2,this.callback)
				
				//导出光学传感器数据
				this.data3.push([{'content':'血压'},{'content':'心率'},{'content':'时间'}])
					for(var i = 0;i<this.xyxlData1[0].data.length;i++){
						var x = this.xyxlData1[0].data[i]
						var y = this.xyxlData1[1].data[i]
						var time = this.xyxlData1[0].date[i]
						var SaveData = [{'content':x},{'content':y},{'content':time}]
						this.data3.push(SaveData)
					}
				doExport(this.value+'光学传感器',this.data3,this.callback)
				
				//导出1-生物电势AFE数据
				this.data4.push([{'content':'一通道'},{'content':'二通道'},{'content':'三通道'},{'content':'四通道'},{'content':'五通道'},{'content':'六通道'},{'content':'七通道'},{'content':'八通道'},{'content':'时间'}])
					for(var i = 0;i<this.AFEData11[0].data.length;i++){
						var yi = this.AFEData11[0].data[i]
						var er = this.AFEData11[1].data[i]
						var san = this.AFEData11[2].data[i]
						var si = this.AFEData11[3].data[i]
						var wu = this.AFEData11[4].data[i]
						var liu = this.AFEData11[5].data[i]
						var qi = this.AFEData11[6].data[i]
						var ba = this.AFEData11[7].data[i]
						var time = this.AFEData11[0].date[i]
						var SaveData = [{'content':yi},{'content':er},{'content':san},{'content':si},{'content':wu},{'content':liu},{'content':qi},{'content':ba},{'content':time}]
						this.data4.push(SaveData)
					}
				doExport(this.value+'光学传感器',this.data4,this.callback)
				
				//导出2-生物电势AFE数据
				this.data5.push([{'content':'一通道'},{'content':'二通道'},{'content':'三通道'},{'content':'四通道'},{'content':'五通道'},{'content':'六通道'},{'content':'七通道'},{'content':'八通道'},{'content':'时间'}])
					for(var i = 0;i<this.AFEData22[0].data.length;i++){
						var yi = this.AFEData22[0].data[i]
						var er = this.AFEData22[1].data[i]
						var san = this.AFEData22[2].data[i]
						var si = this.AFEData22[3].data[i]
						var wu = this.AFEData22[4].data[i]
						var liu = this.AFEData22[5].data[i]
						var qi = this.AFEData22[6].data[i]
						var ba = this.AFEData22[7].data[i]
						var time = this.AFEData22[0].date[i]
						var SaveData = [{'content':yi},{'content':er},{'content':san},{'content':si},{'content':wu},{'content':liu},{'content':qi},{'content':ba},{'content':time}]
						this.data5.push(SaveData)
					}
				doExport(this.value+'光学传感器',this.data5,this.callback)
				
			},
			callback(e){
				console.log('回调函数:'+e);
				
			},
			
			
			open(){
				var path = 'Android/data/io.dcloud.HBuilder/documents/科研Excel'
				// var main = plus.android.runtimeMainActivity();
				// var Intent = plus.android.importClass("android.content.Intent");
				// var mIntent = new Intent('android.settings.SETTINGS');
				// main.startActivity(mIntent);
				plus.runtime.launchApplication(
						{
							pname: 'cn.wps.moffice_eng'  
						},  
						function(e) {  
							console.log('Open system default browser failed: ' + e.message);  
						}  
					);
				

			     },
			//开始采集按钮
			startCJ(){
				
				//动态显示统计图
				
				if(this.value4 == '加速度计'){
					this.chartData.series = this.jsdjData
				}else if(this.value4 == '陀螺仪'){
					this.chartData.series = this.tlyData
				}else if(this.value4 == '磁力计'){
					this.chartData.series = this.cljData
				}else if(this.value4 == '光学传感器'){
					this.chartData.series = this.xyxlData
				}else if(this.value4 == '1-生物电势AFE'){
					this.chartData.series = this.AFEData1
				}else if(this.value4 == '2-生物电势AFE'){
					this.chartData.series = this.AFEData2
				}else if(this.value4 == ''|| this.value4 == null){
					this.$refs.uToast.show({
						title: '请输入传感器名称',
					})
				}
				
			},
			//开始导出
			starts(){
				this.dsSave = true //定时保存打开
				this.disSave = true
				var _this = this
				if(this.value == ''|| this.value == null){
						this.$refs.uToast.show({
							title: '请输入文件名称',
						})
						this.disSave = false
				}else if(this.value1 == this.value3){
					this.$refs.uToast.show({
						title: '采集结束',
					})
					this.ends()
				}else{
					this.$refs.uToast.show({
						title: '开始采集',
					})
					//实时改变数据长度
					//打开开关的状态
					if(this.checked){
						if(this.value3<this.value1){
							this.setIntervalId = setInterval(function(){
								return _this.dataLength()
							},1000)
						}
					}
					//实时改变数据长度
					//关闭开关的状态
					if(!this.checked){
							this.setIntervalId = setInterval(function(){
								return _this.dataLength()
							},1000)
					}
					
					this.disStart = true
					this.disSuspend = false
					this.disEnd = false
				}
			},
			//暂停导出
			suspend(){
				this.dsSave = false //定时保存关闭
				//关闭改变长度
				clearInterval(this.setIntervalId)
				this.disStart = false
				this.disSuspend = true
			},
			//结束导出
			ends(){
				
				this.dsSave = false //定时保存关闭
				this.$refs.uToast.show({
					title: '采集结束',
				})
				//关闭改变长度
				clearInterval(this.setIntervalId)
				this.disSave = false
				this.value = ''
				this.value1 = ''
				this.value3 = ''
				this.checked = false
				this.disStart = false
				this.disSuspend = true
				this.disEnd = true
				//保存文件逻辑
				this.exports()
			},
			//实时改变数据长度
			dataLength(){
				this.value3++
				//时长开关打开的时候
				if(this.checked){
					if(this.value3 == this.value1){
							this.$refs.uToast.show({
								title: '采集结束',
							})
							this.ends()
						//关闭改变长度
						clearInterval(this.setIntervalId)
						//保存文件逻辑
						
					}
				}
			},
			//蓝牙连接提醒
			showToast(toast_info) {
				this.$refs.uToast.show({
					title: toast_info.title,
					type: toast_info.type,
					url: toast_info.url
				})
			},
		},
		watch:{
			//监听开关状态（时长为空的时候，开关无法打开）
			checked(val){
				if(this.checked){
					if(this.value1==null || this.value1 == '' ||this.value1 == 0){
						this.$refs.uToast.show({
							title: '请输入大于0的时长',
						})
						this.checked = false
					}
				}
			},
			//监听时长（时长为空的时候开关自动关闭
			value1(val){
				if(this.value1==null || this.value1 == ''){
					
					this.checked = false
				}
			}
		}
	}
</script>

<style lang="scss">
	.data {
		margin: 20upx;
		display: flex;
		justify-content: space-around;
		.text {
			margin-top: 20upx;
			margin-right: 30upx;
		}
		.text1 {
			margin-top: 20upx;
			margin-right: 85upx;
		}
	}
	.chart {
		width: 100%;
		height: 600upx;
		.charts-box{
		  width: 100%;
		  height:300px;
		}
	}
	.hisBtn {
		margin-top: 5upx;
	}
</style>
