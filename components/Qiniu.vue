<template>
	<div class="qiniu-wrap">
		<div v-if="!src" style="height: 100%;" class="flex column align-center" @click="onUpload">
			<image src="/static/images/file.png" style="width:64rpx;height:64rpx;margin: 30rpx 0 16rpx;" ></image>
			<div class="font10" style="color: #B5B5B5;">{{title}}</div>
		</div>
		<image v-else style="width:100%;height:100%;" :src="src"></image>
	</div>
</template>

<script>
	export default {
		props: {
			title: {
				type: String,
				default: '上传照片'
			}
		},
		data() {
			return {
				src: ''
			};
		},
		methods: {
			onUpload() {
				uni.chooseImage({
					count: 1,
					success: (res) => {
						let {tempFiles} = res
						this.src = tempFiles[0].path
					}
				})
			}
		}
	}
</script>

<style lang="less">
.qiniu-wrap{
	width: 268rpx;
	height: 168rpx;
	border: 2rpx dashed #BEBEBE;
}
</style>
