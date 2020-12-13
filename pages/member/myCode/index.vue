<template>
	<div>
		<div class="tabs">
			<div @click="toggleTab(inx)" class="tab-item pos-r" :class="{active: curTab==inx}" v-for="(i,inx) in tabs" :key="inx">
				<div>{{i}}</div>
				<div class="ind"></div>
			</div>
		</div>
		<template v-if="curTab==0">
			<div class="flex align-center code-item" v-for="(i,inx) in list" :key="inx">
				<img src="" style="width: 90rpx;height: 90rpx;" />
				<div class="left">
					<div style="font-size:14px;color:#393939">{{i.code}}</div>
					<div style="font-size:10px;color:#8B8B8B">{{i.lname}}</div>
				</div>
				<div class="btn" :class="{dis: i.useFor!=0}" @click="onGive(i)">
					{{i.useFor==0?'赠送名额':'已赠送'}}
				</div>
			</div>
		</template>
		
		<Modal :show.sync="showGive" :is-bottom="false">
			<div class="bg-white flex column border5" style="width: 500rpx;height:440rpx;overflow: hidden;">
				<div class="left" style="width: 100%;border-bottom: 1rpx solid #DDDDDD;">
					<div class="flex column align-center">
						<div class="font20" style="color: #393939;margin: 60rpx 0;">确定要赠送么?</div>
						<input class="border5" placeholder="输入赠送人手机号"  style="height: 88rpx;width: 416rpx;background:#F5F5F5;" v-model="giveNumber" />
					</div>
				</div>
				<div class="flex" style="line-height: 100rpx;">
					<div class="left text-center" @click="showGive=false">
						取消
					</div>
					<div style="width: 1rpx;height: 100rpx;background: #DDD;"></div>
					<div class="left text-center" @click="onConfirmGive">
						确定
					</div>
				</div>
			</div>
		</Modal>
	</div>
</template>

<script>
	import Modal from '@/components/Modal'
	export default {
		mounted() {
			this.getData()
		},
		components:{
			Modal
		},
		data() {
			return {
				tabs: ['赠送名额', '赠送记录'],
				curTab: 0,
				
				page: 1,
				list: [],
				isLoading: false,
				isEnd: false,
				
				showGive: false,
				giveNumber: '',
			};
		},
		methods: {
			toggleTab(inx) {
				if (this.curTab != inx) {
					this.curTab = inx
					this.getData(true)
				}
			},
			getData(reset=false) {
				if (reset) {
					this.page = 1
					this.list = []
					this.isEnd = false
				}
				if (this.curTab == 0) {
					let d = {
						page: this.page
					}
					this.isLoading = true
					this.$r.post('/user/levelCodeList', d)
						.then(r => {
							this.list.push(...r.data)
							if (r.data.length < 10) {
								this.isEnd = true
							}
						})
						.finally(_ => {
							this.isLoading = false
						})
				}
			},
			onGive(i) {
				if (i.useFor==0) {
					this.selCode = i
					this.showGive = true
				}
			},
			onConfirmGive() {
				// if (!^/1\d{10}/$.test(this.giveNumber)) {
				// 	return
				// }
				let d = {
					code: this.selCode.code,
					phone: this.giveNumber
				}
				this.$r.post('user/levelCodeSend', d)
					.then(r => {
						this.showGive = false
						uni.showToast({
							title: '赠送成功~~~',
							icon: 'none'
						})
						this.getData(true)
					})
					.catch(e => {
						uni.showToast({
							title: e.msg,
							icon: 'none'
						})
					})
			}
		},
		onReachBottom() {
			if (this.isLoading || this.isEnd) {
				return
			}
			this.page++
			this.getData()
		}
	}
</script>

<style lang="less">
.tabs{
	height: 100rpx;
	background: #fff;
	display: flex;
	.tab-item{
		flex: 1;
		line-height: 100rpx;
		text-align: center;
		color: #8B8B8B;
		font-size: 16px;
		&.active{
			color: #000000;
			.ind{
				background: #EB3426;
			}
		}
		.ind{
			width: 60rpx;
			height: 6rpx;
			background: transparent;
			position: absolute;
			bottom: 0;
			left: 50%;
			margin-left: -30rpx;
		}
	}
}

.code-item{
	height: 140rpx;
	background: #fff;
	border-bottom: 1rpx solid #F2F2F2;
	padding: 0 40rpx;
	.btn{
		width: 140rpx;
		height: 48rpx;
		text-align: center;
		border: 1rpx solid #EB3426;
		border-radius: 24rpx;
		line-height: 46rpx;
		background: #fff;
		color: #EB3426;
		font-size: 12px;
		&.dis{
			background: #DCDCDC;
			border-color: #DCDCDC;
			color: #4E4E4E;
		}
	}
}
</style>

