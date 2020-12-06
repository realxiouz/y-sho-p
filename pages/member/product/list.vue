<template>
	<div>
		<div class="bg-white p13-h" v-for="(i,inx) in list" :key="inx">
			<div style="padding-top:36rpx;">
				<div class="flex">
					<image :src="i.image" style="width: 220rpx;height:162rpx;margin-right:20rpx;"></image>
					<div class="left">
						<div class="text-cut font14" style="color: #393939;">{{i.storeName}}</div>
						<div class="flex" style="margin: 10rpx 0;">
							<div class="text-center font12" style="width:160rpx;line-height:48rpx;border-radius:24rpx;color:#B2B2B2;border:1rpx solid #B2B2B2;">{{i.unitName}}</div>
						</div>
						<div class="flex align-center">
							<div class="font16" style="color:#EB3425">¥{{i.price}}</div>
							<div class="left"></div>
							<div class="font14" style="padding:0rpx 40rpx;line-height:60rpx;border-radius:30rpx;background:#EC4639;color:#fff;" @click="onAdd(i)">
								加入购物车
							</div>
						</div>
					</div>
				</div>
				<div class="font12" style="color:#B2B2B2;padding: 10rpx 0">库存:{{i.stock}}</div>
			</div>
		</div>
		
		<div style="height:120rpx;"></div>
		<div class="fixed-wrap flex align-center justify-between p13-h">
			<!-- <div class="font14" style="color:#393939">
				已选
				<span style="color:#E53636;margin-left: 28rpx;">{{1}}</span>
				商品
			</div> -->
			<div></div>
			<div class="bottom-btn red" @click="onPay">去支付</div>
		</div>
	</div>
</template>

<script>
	export default {
		mounted() {
			let type = this._route.query.type || 3
			this.$r.get(`products?typeKh=${type}`)
				.then(r => {
					this.list = r.data
				})
		},
		data() {
			return {
				list: [],
			}
		},
		methods: {
			onAdd(i) {
				let d = {
					productId: i.id,
          cartNum: 1,
          new: 0,
          uniqueId: ''
				}
				this.$r.post('/cart/add', d)
					.then(r => {
						uni.showToast({
							icon: 'none',
							title: '商品添加成功'
						})
					})
			},
			onPay() {
				this.$yrouter.switchTab('/pages/shop/ShoppingCart/index')
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
