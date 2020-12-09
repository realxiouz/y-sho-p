<template>
	<div>
		<div class="text-center">
			<div style="font-size: 56rpx;color: #000;margin-top: 90rpx;">开通个人账户</div>
			<div style="font-size: 14px;color: #000;margin-top: 18rpx;">开通账户，尊享福利</div>
		</div>
		<div
			style="width:672rpx;height: 112rpx;box-sizing: border-box;
			background: linear-gradient(184deg, #E5E5E5 0%, #CECECE 26%, #ECECEC 52%, #9797A4 90%, #A0A0B1 100%);margin: 40rpx auto;"
			class="flex align-center p13-h">
			<div class="left">已选级别</div>
			<picker :range="levels" @change="onLevelChange" range-key="name">
				{{ levels.length ? levels[selLevel].name : '还未选择'}}
			</picker>
		</div>
		<div class="flex align-center justify-between" style="width: 672rpx;height: 162rpx;margin: 0 auto;border:1rpx solid #E2E2E2;padding: 0 50rpx;box-sizing: border-box;">
			<div style="font-size: 36rpx;color: #434343;">
				{{ levels.length ? levels[selLevel].name : '还未选择'}}
			</div>
			<div class="text-price" style="font-size: 40rpx;color: #EB3426;">{{levels.length ? levels[selLevel].money : '还未选择'}}</div>
		</div>
		
		<div
			@click="$yrouter.push({
			  path: '/pages/member/person/open',
			  query: {openType: 0, level: selLevel}
			})"
			style="width: 520rpx;line-height:88rpx;color: #fff;background: #EC4639;text-align: center;border-radius: 10rpx;margin: 200rpx auto 0;"
		>马上开启</div>
		<div
			@click="$yrouter.push({
			  path: '/pages/member/person/open',
			  query: {openType: 1, level: selLevel}
			})"
			style="width: 520rpx;line-height:88rpx;color: #8B8B8B;text-align: center;border-radius: 10rpx;margin: 0rpx auto;"
		>激活码开启</div>
	</div>
	
</template>

<script>
export default {
	mounted() {
		this.$r.get('user/level/list?type=1', {login: false})
			.then(r => {
				this.levels = r.data
			})
	},
	data() {
		return {
			levels: [],
			selLevel: 0,
		}
	},
	methods: {
		onLevelChange(e) {
			this.selLevel = e.detail.value
		},
	}
}
</script>

<style>
</style>
