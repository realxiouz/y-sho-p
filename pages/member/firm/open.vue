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
		<div class="font12 p13-h" style="line-height: 98rpx;background:#F7F7F7;color: #393939;">
			{{txtArr[step]}}
		</div>
		<template v-if="step==0">
			<div class="addAddress absolute">
				<view class="list">
				  <view class="item acea-row row-between-wrapper">
						<view class="name">姓名</view>
						<input type="text" placeholder="请输入真实姓名" v-model="ownerName" required />
				  </view>
				  <view class="item acea-row row-between-wrapper">
						<view class="name">联系电话</view>
						<input type="text" placeholder="请输入联系电话" v-model="phone" required />
				  </view>
					<view class="item acea-row row-between-wrapper flex">
						<div class="name">验证码</div>
						<div class="flex left">
							<input type="text" placeholder="请输入验证码" v-model="code" required />
							<div style="width:80px;" @click="onCode">发送</div>
						</div>
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
				</view>
			</div>
		</template>
		<template v-if="step==1">
			<div class="bg-white mb5 flex align-center p13-h" style="height: 88rpx;">
				<div>资质类型</div>
				<div class="left"></div>
				<picker :range="types" @change="onTypeChange" class="flex">{{types[selType]}}</picker>
			</div>
			<div class="bg-white mb5" style="padding: 0 90rpx;">
				<div class="up-title">
					上传营业执照<span class="sm">（彩色扫描件或数码照片）</span>
				</div>
				<Qiniu title="上传营业执照" v-model="picYyzz"></Qiniu>
				<div class="up-title">
					上传法人身份证<span class="sm">（彩色扫描件或数码照片）</span>
				</div>
				<div class="flex">
					<Qiniu title="上传法人身份证人像面" v-model="picFrrx"></Qiniu>
					<div style="width: 30rpx;"></div>
					<Qiniu title="上传法人身份证国徽面" v-model="picFrgh"></Qiniu>
				</div>
				<template v-if="selType==0">
					<div class="up-title">
						其他证件<span class="sm">（彩色扫描件或数码照片）</span>
					</div>
					<Qiniu v-model="picQt"></Qiniu>
				</template>
				<div style="height: 30rpx;"></div>
			</div>
			<div class="addAddress absolute">
				<view class="list">
				  <view class="item acea-row row-between-wrapper">
						<view class="name">账户类型</view>

						<picker :range="accountTypes" range-key="value"  @change="onAccountTypeChange" class="flex">
							{{accountTypes.length ? accountTypes[selAccountType].value: '还未选择'}}
						</picker>
				  </view>
				  <view class="item acea-row row-between-wrapper">
						<view class="name">开户行</view>
						<input type="text" placeholder="请输入联系电话" v-model="bankName" required />
				  </view>
					<view class="item acea-row row-between-wrapper">
						<view class="name">开户支行</view>
						<input type="text" placeholder="请输入联系电话" v-model="bankZh" required />
				  </view>
				  
				  <view class="item acea-row row-between-wrapper">
						<view class="name">银行账号</view>
						<input type="text" placeholder="请填写具体地址" v-model="bankAccount" required />
				  </view>
				</view>
			</div>
		</template>
		<template v-if="step==2">
			<div class="addAddress absolute mb5">
				<view class="list">
				  <view class="item acea-row row-between-wrapper">
					<view class="name">店铺名称</view>
					<input type="text" placeholder="请输入店铺名称" v-model="name" required />
				  </view>
				  <view class="item acea-row row-between-wrapper" @click="onGis">
					<view class="name">店铺位置</view>
					<div class="flex" style="width:220px">
						<div class="left text-cut">{{address?address:'选择位置'}}</div>
						<view class="iconfont icon-jiantou"></view>
					</div>
				  </view>
				 </view>
			</div>
			<div class="bg-white mb5" style="padding: 0 90rpx;">
				<div class="up-title">
					上传店铺门头照片
				</div>
				<Qiniu title="上传门头照" v-model="picDpmt"></Qiniu>
				<div style="height: 30rpx;"></div>
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
			
			this.$r.get('/dictDetailList?dictId=8')
				.then(r => {
					this.types = r.data.map(i => i.value)
				})

			this.$r.get('dictDetailList?dictId=9')
				.then(r => {
					this.accountTypes = r.data
				})

			this.$r.get('user/level/list?type=2')
				.then(r => {
					this.levels = r.data
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
				districts: [],
				addressText: '',
				step: 0,
				
				showPay: false,
				
				types: [],
				selType: 0,

				accountTypes: [],
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
				ownerName: '',
				ownerSex: '',
				phone: '',
				code: '',
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
				if (this.step == 3) {
					let d = {
						name: this.name,
						longitude: this.longitude,
						latitude: this.latitude,
						picDpmt: this.picDpmt,
						address: this.address,
						zjlx: this.types[this.selType],
						picYyzz: this.picYyzz,
						picFrrx: this.picFrrx,
						picFrgh: this.picFrgh,
						picQt: this.picQt,
						bankName: this.bankName,
						bankZh: this.bankZh,
						bankAccount: this.bankAccount,
						ownerName: this.ownerName,
						phone: this.phone,
						code: this.code,
						ownerSex: '男',
						level: this.levels[this.selLevel].id,
						bankType: this.accountTypes[this.selAccountType].value,
						
						province: this.province,
						city: this.city,
						district: this.district,
					}
					console.log(d)
					this.$r.post('/store/applyAdd', d)
						.then(r => {
							this.$store.commit('setMember', {
								applyLevel: this.levels[this.selLevel].id,
								type: 3,
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
			onGis() {
				uni.chooseLocation({
					success: (res) => {
						console.log(res)
						let { address, name, latitude, longitude} = res
						this.address = name
						this.latitude = latitude
						this.longitude = longitude
					}
				})
			},
			onConfirmPay() {
				console.log('111')
			},
			onTypeChange(e) {
				this.selType = e.detail.value
			},
			onAccountTypeChange(e) {
				this.selAccountType = e.detail.value
			},
			onLevelChange(e) {
				this.selLevel = e.detail.value
			},
			onCode() {
				if (!/^1\d{10}$/.test(this.phone)) {
					uni.showToast({
						icon: 'none',
						title: '填写正确的手机号码'
					})
					return
				}
				this.$r.post('/sms/send', {
					phone: this.phone,
					type: 'auth'
				}).then(r => {
					uni.showToast({
						icon: 'none',
						title: '验证码已发送'
					})
				}).catch(e => {
					uni.showToast({
						icon: 'none',
						title: e.msg
					})
				})
			},
			validateStep(step) {
				if (step == 0) {
					if (!this.ownerName) {
						uni.showToast({
							icon: 'none',
							title: '填写联系人'
						})
						return false
					}
					if (!/^1\d{10}$/.test(this.phone)) {
						uni.showToast({
							icon: 'none',
							title: '填写正确的手机号码'
						})
						return false
					}
					if (!this.code) {
						uni.showToast({
							icon: 'none',
							title: '填写验证码'
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
