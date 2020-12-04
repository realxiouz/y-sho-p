<template>
	<div>
		<div class="flex align-center" v-for="(i,inx) in list" :key="inx">
			<div></div>
			<div class="left">
				<div class="flex">
					<image :src="i.image" style="width: 220rpx;height:162rpx;"></image>
					<div class="left">
						<div class="text-cut font14" style="color: #393939;">{{i.storeName}}</div>
						<div>{{i.unitName}}</div>
						<div class="flex align-center">
							<div>{{i.price}}</div>
							<div class="left"></div>
							<div>
								<view class="carnum acea-row row-center-wrapper">
								  <view class="reduce" :class="list[inx].cartNum <= 1 ? 'on' : ''"
								    @click.prevent="reduce(inx)">-</view>
								  <view class="num">{{ i.cartNum }}</view>
								  <view class="plus" :class="list[inx].cartNum >= list[inx].stock ? 'on' : ''"
								    @click.prevent="plus(inx)">+</view>
								</view>
							</div>
						</div>
					</div>
				</div>
				<div>库存:{{i.stock}}</div>
			</div>
		</div>
		
		<div style="height:120rpx;"></div>
		<div class="fixed-wrap flex align-center justify-between p13-h">
			<div class="font14" style="color:#393939">
				已选
				<span style="color:#E53636;margin-left: 28rpx;">{{1}}</span>
				商品
			</div>
			<div class="bottom-btn red" @click="onAddStep">确认选择</div>
		</div>
	</div>
</template>

<script>
	export default {
		mounted() {
			this.$r.get('products?typeKh=3')
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
