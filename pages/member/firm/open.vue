<template>
	<div>
		<div style="height: 112rpx;background: linear-gradient(184deg, #E5E5E5 0%, #CECECE 26%, #ECECEC 52%, #9797A4 90%, #A0A0B1 100%);" class="flex align-center">
			
		</div>
		<div class="font12 p13-h" style="line-height: 98rpx;background:#F7F7F7;color: #393939;">
			{{txtArr[step]}}
		</div>
		<template v-if="step==0">
			<div class="addAddress absolute">
				<view class="list">
				  <view class="item acea-row row-between-wrapper">
					<view class="name">姓名</view>
					<input type="text" placeholder="请输入姓名" v-model="userAddress.realName" required />
				  </view>
				  <view class="item acea-row row-between-wrapper">
					<view class="name">联系电话</view>
					<input type="text" placeholder="请输入联系电话" v-model="userAddress.phone" required />
				  </view>
				  <view class="item acea-row row-between-wrapper">
					<view class="name">所在地区</view>
					<view class="picker acea-row row-between-wrapper select-value form-control">
					  <view class="address">
						<CitySelect ref="cityselect" :defaultValue="addressText" @callback="result" :items="district"></CitySelect>
					  </view>
					  <view class="iconfont icon-dizhi font-color-red"></view>
					</view>
				  </view>
				  <view class="item acea-row row-between-wrapper">
					<view class="name">详细地址</view>
					<input type="text" placeholder="请填写具体地址" v-model="userAddress.detail" required />
				  </view>
				</view>
			</div>
		</template>
		<template v-if="step==1">
			<div class="bg-white mb5" style="height: 88rpx;">
				
			</div>
			<div class="bg-white mb5" style="padding: 0 90rpx;">
				<div class="up-title">
					上传营业执照<span class="sm">（彩色扫描件或数码照片）</span>
				</div>
				<Qiniu title="上传营业执照"></Qiniu>
				<div class="up-title">
					上传法人身份证<span class="sm">（彩色扫描件或数码照片）</span>
				</div>
				<div class="flex">
					<Qiniu title="上传法人身份证正面"></Qiniu>
					<div style="width: 30rpx;"></div>
					<Qiniu title="上传法人身份证正面"></Qiniu>
				</div>
				<template v-if="true">
					<div class="up-title">
						其他证件<span class="sm">（彩色扫描件或数码照片）</span>
					</div>
					<Qiniu></Qiniu>
				</template>
				<div style="height: 30rpx;"></div>
			</div>
			<div class="addAddress absolute">
				<view class="list">
				  <view class="item acea-row row-between-wrapper">
					<view class="name">姓名</view>
					<input type="text" placeholder="请输入姓名" v-model="userAddress.realName" required />
				  </view>
				  <view class="item acea-row row-between-wrapper">
					<view class="name">联系电话</view>
					<input type="text" placeholder="请输入联系电话" v-model="userAddress.phone" required />
				  </view>
				  <view class="item acea-row row-between-wrapper">
					<view class="name">所在地区</view>
					<view class="picker acea-row row-between-wrapper select-value form-control">
					  <view class="address">
						<CitySelect ref="cityselect" :defaultValue="addressText" @callback="result" :items="district"></CitySelect>
					  </view>
					  <view class="iconfont icon-dizhi font-color-red"></view>
					</view>
				  </view>
				  <view class="item acea-row row-between-wrapper">
					<view class="name">详细地址</view>
					<input type="text" placeholder="请填写具体地址" v-model="userAddress.detail" required />
				  </view>
				</view>
			</div>
		</template>
		<template v-if="step==2">
			<div class="addAddress absolute mb5">
				<view class="list">
				  <view class="item acea-row row-between-wrapper">
					<view class="name">店铺名称</view>
					<input type="text" placeholder="请输入姓名" v-model="userAddress.realName" required />
				  </view>
				  <view class="item acea-row row-between-wrapper" @click="onGis">
					<view class="name">店铺位置</view>
					<div class="flex">
						<div>{{address?address:'选择位置'}}</div>
						<view class="iconfont icon-jiantou"></view>
					</div>
				  </view>
				 </view>
			</div>
			<div class="bg-white mb5" style="padding: 0 90rpx;">
				<div class="up-title">
					上传店铺门头照片
				</div>
				<Qiniu title="上传执照"></Qiniu>
				<div style="height: 30rpx;"></div>
			</div>
		</template>
		<template v-if="step==3">
			<div @click="$yrouter.push('/pages/member/product/list')">选择商品</div>
		</template>
		<div style="height:120rpx;"></div>
		<div class="fixed-wrap flex align-center justify-around">
			<div class="bottom-btn" @click="onMinusStep">上一步</div>
			<div class="bottom-btn red" @click="onAddStep">下一步</div>
		</div>
		
		<Modal :show.sync="showPay" :is-bottom="false">
			<div class="bg-white flex column border5" style="width: 500rpx;height:440rpx;overflow: hidden;">
				<div class="left" style="width: 100%;border-bottom: 1rpx solid #DDDDDD;">
					<div class="flex column align-center">
						<div class="font20" style="color: #393939;margin: 60rpx 0;">支付密码</div>
						<input class="border5"  style="height: 88rpx;width: 416rpx;background:#F5F5F5;" type="password" value="" />
					</div>
				</div>
				<div class="flex" style="line-height: 100rpx;">
					<div class="left text-center" @click="showPay=false">
						取消
					</div>
					<div style="width: 1rpx;height: 100rpx;background: #DDD;"></div>
					<div class="left text-center" @click="onConfirmPay">
						确定
					</div>
				</div>
			</div>
		</Modal>
	</div>
</template>

<script>
	import CitySelect from "@/components/CitySelect";
	import { getAddress, postAddress, getCity } from "@/api/user";
	import Qiniu from '@/components/Qiniu'
	import Modal from '@/components/Modal'
	
	export default {
		mounted() {
			this.getCityList()
			
			this.$r.get('agent/level/list')
				.then(r => {
					console.log(r)
				})
		},
		data() {
			return {
				txtArr: [
					'个人联系方式',
					'填写/上传资质',
					'店铺信息'
				],
				userAddress: {},
				district: [],
				addressText: '',
				step: 0,
				
				address: '',
				showPay: false,
			}
		},
		components: {
		  CitySelect,
		  Qiniu,
		  Modal,
		},
		methods: {
			getCityList() {
				getCity()
				  .then(res => {
				    this.district = res.data;
				    this.ready = true;
				  })
				  .catch(err => {
				    uni.showToast({
						title: err.msg,
						icon: "none",
						duration: 2000,
					});
				  });
			},
			result(values) {
			  this.address = {
			    province: values.province.name || "",
			    city: values.city.name || "",
			    district: values.district.name || "",
			    city_id: values.city.id
			  };
			  this.addressText = `${this.address.province}${this.address.city}${this.address.district}`;
			},
			onAddStep() {
				this.step++
				if (this.step == 3) {
					this.showPay = true
				}
			},
			onMinusStep() {
				this.step--
			},
			onGis() {
				uni.chooseLocation({
					success: (res) => {
						console.log(res)
						let { address, name, latitude, longitude} = res
						this.address = name
					}
				})
			},
			onConfirmPay() {
				console.log('111')
			}
		}
	}
</script>

<style lang="less">
.up-title{
	padding: 20rpx 0;
	color: #404040;
	font-size: 18px;
	.sm{
		font-size: 12px;
		color: #ccc;
	}
}
.fixed-wrap{
	position: fixed;
	z-index: 10;
	left: 0;
	right: 0;
	bottom: 0;
	height: 120rpx;
	background: #fff;
	.bottom-btn{
		height: 88rpx;
		line-height: 88rpx;
		width: 264rpx;
		text-align: center;
		line-height: 88rpx;
		font-size: 16px;
		border-radius: 10rpx;
		background: #F2F2F2;
		color: #8B8B8B;
		&.red{
			background: #EC4639;
			color: #fff;
		}
	}
}
</style>
