<template>
	<div>
		<div style="height: 112rpx;" class="flex align-center">
			
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

<style lang="less" scoped>
</style>
