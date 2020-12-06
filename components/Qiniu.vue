<template>
	<div class="qiniu-wrap">
		<div v-if="!value" style="height: 100%;" class="flex column align-center" @click="onUpload">
			<image src="/static/images/file.png" style="width:64rpx;height:64rpx;margin: 30rpx 0 16rpx;" ></image>
			<div class="font10" style="color: #B5B5B5;">{{title}}</div>
		</div>
		<image v-else style="width:100%;height:100%;" :src="value"></image>
	</div>
</template>

<script>
	import * as qiniu from 'qiniu-js'
	
	export default {
		props: {
			title: {
				type: String,
				default: '上传照片'
			},
			value: {
				type: String,
			}
		},
		data() {
			return {
			};
		},
		methods: {
			onUpload() {
				uni.chooseImage({
					count: 1,
					success: (res) => {
						let {tempFiles} = res
						const config = {
						  useCdnDomain: true,
						  region: qiniu.region.z2
						}
						
						this.$r.post('api/upload/getTokenQiniu', {
							fileName: tempFiles[0].name,
						})
							.then(r => {
								const {domain, token, key} = r.data
								const observable = qiniu.upload(tempFiles[0], key, token, null, config)
								const subscription = observable.subscribe(r => {}, e => {
									console.log(e)
								}, r => {
									console.log(r)
									this.$emit('input', `${domain}/${r.key}`)
								})
							})
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
