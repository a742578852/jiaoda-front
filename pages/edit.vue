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
				<u-slider v-model="value2" ></u-slider>
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
				<u-slider v-model="value3" ></u-slider>
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
				<u-slider v-model="value4" ></u-slider>
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
				<u-slider v-model="value9"  step="12.5"></u-slider>
			</view>
		</view>
		<view class="sliders" v-if="checked9">
			<text>采集频率(Hz):{{value10*25}}</text>
			<view class="slider" >
				<u-slider v-model="value10" ></u-slider>
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
				<u-slider v-model="value11"  step="12.5"></u-slider>
			</view>
		</view>
		<view class="sliders" v-if="checked10">
			<text>采集频率(Hz):{{value12*25}}</text>
			<view class="slider" >
				<u-slider v-model="value12" ></u-slider>
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
				checked1:false,
				checked2:false,
				checked3:false,
				checked4:false,
				checked5:false,
				checked6:false,
				checked7:false,
				checked8:false,
				checked9:false,
				checked10:false,
				value1:0,
				value2:0,
				value3:0,
				value4:0,
				value9:0,
				value10:0,
				value11:0,
				value12:0,
				uToast_info:{},
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
			toData(){
				var _this = this
				//监听蓝牙是否可用
				uni.openBluetoothAdapter({
				  success(res) {
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
			this.linked = uni.getStorageSync('link_ble_info').name
			//传感器配置回显
			this.checked1 = uni.getStorageSync('checked1')
			this.checked2 = uni.getStorageSync('checked2')
			this.checked3 = uni.getStorageSync('checked3')
			this.checked4 = uni.getStorageSync('checked4')
			this.checked5 = uni.getStorageSync('checked5')
			this.checked6 = uni.getStorageSync('checked6')
			this.checked7 = uni.getStorageSync('checked7')
			this.checked8 = uni.getStorageSync('checked8')
			this.checked9 = uni.getStorageSync('checked9')
			this.checked10 = uni.getStorageSync('checked10')
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
				uni.setStorageSync('checked5',val)
				// if(val){
				// 	this.checked6 = false
				// 	this.checked7 = false
				// 	this.checked8 = false
				// }
			},
			checked6(val){
				uni.setStorageSync('checked6',val)
				// if(val){
				// 	this.checked5 = false
				// 	this.checked7 = false
				// 	this.checked8 = false
				// }
			},
			checked7(val){
				uni.setStorageSync('checked7',val)
				// if(val){
				// 	this.checked6 = false
				// 	this.checked5 = false
				// 	this.checked8 = false
				// }
			},
			checked8(val){
				uni.setStorageSync('checked8',val)
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
