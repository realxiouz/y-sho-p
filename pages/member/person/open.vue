<template>
	<div>
		<div style="height: 112rpx;background: linear-gradient(184deg, #E5E5E5 0%, #CECECE 26%, #ECECEC 52%, #9797A4 90%, #A0A0B1 100%);" class="flex align-center">
			
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
			<div>
				
			</div>
		</template>
		
		<div class="fixed-wrap flex align-center justify-around">
			<div class="bottom-btn">上一步</div>
			<div class="bottom-btn red">下一步</div>
		</div>
	</div>
</template>

<script>
	import CitySelect from "@/components/CitySelect";
	import { getAddress, postAddress, getCity } from "@/api/user";
	
	export default {
		mounted() {
			this.getCityList()
		},
		data() {
			return {
				userAddress: {},
				district: [],
				addressText: '',
				step: 0,
			}
		},
		components: {
		  CitySelect
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
			}
		}
	}
</script>

<style lang="less">
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
