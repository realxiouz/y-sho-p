<template>
	<div>
		<div class="tabs">
			<div @click="toggleTab(inx)" class="tab-item pos-r" :class="{active: curTab==inx}" v-for="(i,inx) in tabs" :key="inx">
				<div>{{i}}</div>
				<div class="ind"></div>
			</div>
		</div>
		<template v-if="curTab==0">
			<div class="text-center" style="line-height:100rpx;color:#7D7D7D;font-size:12px">
				共有{{userCount}}位成员
			</div>
			<div class="flex align-center code-item" v-for="(i,inx) in list" :key="inx">
				<img :src="i.avatar" style="width: 90rpx;height: 90rpx;margin-right:10rpx" />
				<div class="left">
					<div style="font-size:14px;color:#393939">{{i.username}}</div>
					<div style="font-size:10px;color:#8B8B8B">{{i.levelGrName}}</div>
				</div>
				<div class="btn" :class="{dis: i.statusSend!=0}" @click="onGive(i)">
					{{i.statusSend==0?'赠送名额':'已赠送'}}
				</div>
			</div>
		</template>
		<template v-if="curTab==1">
			<div class="his-item" v-for="(i,inx) in list" :key="inx">
				<div class="flex">
					<div class="left" style="font-size:14px;color:#373737">{{`${i.updateTime}赠送了${i.useForName} ${i.lname}`}}</div>
					<div style="font-size:12px;" :style="{color: cMap[i.status]}">{{sMap[i.status]}}</div>
				</div>
				<div style="font-size:10px;color:#F2F2F2">有效期:{{`${getDay(i.updateTime)}---${getDay(i.useForEndTime)}`}}</div>
			</div>
		</template>
		<Modal :show.sync="showGive" :is-bottom="false">
			<div class="bg-white flex column border5" style="width: 500rpx;height:440rpx;overflow: hidden;">
				<div class="left" style="width: 100%;border-bottom: 1rpx solid #DDDDDD;">
					<div class="flex column align-center">
						<div class="font20" style="color: #393939;margin: 60rpx 0;">确定要赠送么?</div>
						<div class="border5" style="line-height: 88rpx;width: 416rpx;">您有{{codeCount}}个赠送名额</div>
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
				selCode: {},
				codeCount: 0,
				userCount: 0,
				sMap: {
					'0':'未使用',
					'1':'已使用',
					'2':'已禁用',
					'3':'已过期'
				},
				cMap: {					'0':'#617ECB',					'1':'#AAAAAA',					'2':'#E37A7A',					'3':'#E37A7A'				}
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
					this.$r.post('/user/levelCodeUserList', d)
						.then(r => {
							this.codeCount = r.data.codeCount
							this.userCount = r.data.userCount
							this.list.push(...r.data.list)
							if (r.data.list.length < 10) {
								this.isEnd = true
							}
						})
						.finally(_ => {
							this.isLoading = false
						})
				} else if (this.curTab == 1) {
					let d = {
						page: this.page,
						statusSend: 1,
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
				if (i.statusSend==0) {
					this.selCode = i
					this.showGive = true
				}
			},
			onConfirmGive() {
				// if (!^/1\d{10}/$.test(this.giveNumber)) {
				// 	return
				// }
				let d = {
					uid: this.selCode.uid
				}
				this.$r.post('/user/levelCodeSend', d)
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
			},
			getDay(t) {
				return t.split(' ')[0]
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

.his-item{
	background: #fff;
	border-bottom: 1rpx solid #F2F2F2;
	padding: 30rpx 40rpx 20rpx;
}
</style>

