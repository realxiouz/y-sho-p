<template>
	<div>
		<div
			style="height: 112rpx;background: linear-gradient(184deg, #E5E5E5 0%, #CECECE 26%, #ECECEC 52%, #9797A4 90%, #A0A0B1 100%);"
			class="flex align-center p13-h">
			<div class="left">已选级别</div>
			<picker :range="levels" @change="onLevelChange" range-key="name">
				{{ levels.length ? levels[selLevel].name : '还未选择'}}
			</picker>
		</div>
		<template v-if="step==0">
			<div class="addAddress absolute">
				<view class="list">
				  <view class="item acea-row row-between-wrapper">
						<view class="name">姓名</view>
						<input type="text" placeholder="请输入真实姓名" v-model="name" required />
				  </view>
					<view class="item acea-row row-between-wrapper">
						<view class="name">证件类型</view>
						<div class="picker acea-row row-between-wrapper select-value form-control">
							<picker :range="['身份证']">
								{{ '身份证' }}
							</picker>
						</div>
				  </view>
				  <view class="item acea-row row-between-wrapper">
						<view class="name">证件号码</view>
						<input type="text" placeholder="请输入证件号码" v-model="zjhm" required />
				  </view>
				  <view class="item acea-row row-between-wrapper">
						<view class="name">所在地区</view>
						<view class="picker acea-row row-between-wrapper select-value form-control">
						  <view class="address">
							<CitySelect ref="cityselect" :defaultValue="addressText" @callback="result" :items="districts"></CitySelect>
						  </view>
						  <view class="iconfont icon-dizhi font-color-red"></view>
						</view>
				  </view>
					<view class="item acea-row row-between-wrapper">
						<view class="name">详细地址</view>
						<input type="text" placeholder="请输入详细地址" v-model="address" required />
				  </view>
				</view>
			</div>
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

			this.$r.get('user/level/list?type=1')
				.then(r => {
					this.levels = r.data
				})
		},
		data() {
			return {
				districts: [],
				addressText: '',
				step: 0,
				
				showPay: false,
				
				types: [],
				selType: 0,

				selAccountType: 0,

				levels: [],
				selLevel: 0,

				bankAccount: '',
				bankName: '',
				bankZh: '',
				picYyzz: '',
				picFrgh: '',
				picFrrx: '',
				picQt: '',
				picDpmt: '',
				address: '',
				latitude: '',
				longitude: '',
				name: '',
				name: '',
				ownerSex: '',
				zjhm: '',
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
				    this.districts = res.data;
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
				console.log(values)
			  this.addressObj = {
			    province: values.province.name || "",
			    city: values.city.name || "",
			    district: values.district.name || "",
			    city_id: values.city.id
			  };
			  this.addressText = `${this.addressObj.province}${this.addressObj.city}${this.addressObj.district}`;
			  this.province = values.province.id
			  this.district = values.district.id
			  this.city = values.city.id
			},
			onAddStep() {
				if (this.validateStep(this.step)) {
					this.step++
				}
				if (this.step == 1) {
					let d = {
						name: this.name,
						zjlx: '身份证',
						zjhm: this.zjhm,
						level: this.levels[this.selLevel].id,
						
						province: this.province,
						city: this.city,
						district: this.district,
						address: this.address,
					}
					console.log(d)
					this.$r.post('/person/applyAdd', d)
						.then(r => {
							this.$store.commit('setMember', {
								applyLevel: this.levels[this.selLevel].id,
								type: 2,
								apply: r.data.id,
							})
							this.$yrouter.push('/pages/member/product/list')
						})
						.finally(_ => {
							// this.$yrouter.push('/pages/member/product/list')
						})
				}
			},
			onMinusStep() {
				if (this.step==0) {
					return
				}
				this.step--
			},
			onConfirmPay() {
				console.log('111')
			},
			onLevelChange(e) {
				this.selLevel = e.detail.value
			},
			validateStep(step) {
				if (step == 0) {
					if (!this.name) {
						uni.showToast({
							icon: 'none',
							title: '填写联系人'
						})
						return false
					}
					if (!this.zjhm) {
						uni.showToast({
							icon: 'none',
							title: '填写身份证号'
						})
						return false
					}
					if (!this.province||!this.city||!this.district) {
						uni.showToast({
							icon: 'none',
							title: '还未选择地址'
						})
						return false
					}
					if (!this.address) {
						uni.showToast({
							icon: 'none',
							title: '填写地址'
						})
						return false
					}
					return true
				} else if (step == 1) {
					if (!this.picYyzz) {
						uni.showToast({
							icon: 'none',
							title: '还没有上传营业执照'
						})
						return false
					}
					if (!this.picFrgh || !this.picFrrx) {
						uni.showToast({
							icon: 'none',
							title: '还没有上传身份证'
						})
						return false
					}
					if (!this.picQt&&this.selType==0) {
						uni.showToast({
							icon: 'none',
							title: '还没有上传其他证件'
						})
						return false
					}
					if (!this.bankName) {
						uni.showToast({
							icon: 'none',
							title: '还没有填写开户行'
						})
						return false
					}
					if (!this.bankZh) {
						uni.showToast({
							icon: 'none',
							title: '还没有填写开户行支行'
						})
						return false
					}
					if (!this.bankAccount) {
						uni.showToast({
							icon: 'none',
							title: '还没有填写银行账号'
						})
						return false
					}
					return true
				} else if (step == 2) {
					if (!this.name) {
						uni.showToast({
							icon: 'none',
							title: '填写店铺名称'
						})
						return false
					}
					if (!this.latitude) {
						uni.showToast({
							icon: 'none',
							title: '还没有选择地址'
						})
						return false
					}
					if (!this.picDpmt) {
						uni.showToast({
							icon: 'none',
							title: '上传店铺门头照片'
						})
						return false
					}
					return true
				}
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
