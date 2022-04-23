<template>
	<view class="internship_page">
		<view class="inter_wrap">
			<view class="inter_title">管理实习经历</view>
			<view class="inter_item">
				<view class="title">公司名称</view>
				<input type="text" v-model="companyName" />
			</view>
			<view class="inter_item">
				<view class="title">担任职位</view>
				<input type="text" v-model="postion" />
			</view>

			<view class="inter_item">
				<view class="title">开始时间</view>
				<picker mode="date" :value="startDate" start="1970-01-01" @change="bindStartDateChange">
					<label>{{ startDate }}</label>
				</picker>
				<view class="iconfont icon-xuanzeqixiayige"></view>
			</view>

			<view class="inter_item">
				<view class="title">结束时间</view>
				<picker mode="date" :value="endDate" start="1970-01-01" @change="bindEndDateChange">
					<label>{{ endDate }}</label>
				</picker>
				<view class="iconfont icon-xuanzeqixiayige"></view>
			</view>

			<view >
				<view class="title">实习工作描述</view>
				<rich-text-editor v-model="description"></rich-text-editor>
			</view>
		</view>
		
		<button class="btn_remove" @click="deleteEduExp">删除</button>
		<button class="btn_save" @click="saveEduExp">保存</button>
	</view>
</template>

<script>
	import RichTextEditor from "@/components/RichTextEditor.vue"
	export default {
		components: {
			RichTextEditor
		},
		data() {
			return {
				companyName:'',
				postion:'',
				startDate:'2021-01-01',
				endDate: '2021-01-01',
				description:'',
				id:0
			}
		},
		onLoad(option){
			this.id = option.id;
			if(this.id != 0){
				let intExp = wx.getStorageSync('resume').intExps;
				var index = intExp.findIndex(v => v.id == this.id);
				this.companyName = intExp[index].companyName;
				this.postion = intExp[index].postion;
				this.startDate = intExp[index].startDate;
				this.endDate = intExp[index].endDate;
				this.description = intExp[index].description;
			}
		},
		methods: {
			deleteEduExp:function(){
				console.log(this.id)
				if(this.id == 0){
					this.$toast('先保存才能删除哦', 1000, 'none', true);
				}else{
					this.$post('resume/deleteIntExp',{
						id:this.id
					}).then(res => {
					console.log(res)
					if (res.code == 200) {
						if (res.result == 'success') {
							//更新缓存
							wx.setStorageSync('resume',res.data);
							this.$toast('删除成功', 1000, 'none', true);
							setTimeout(() => {
								uni.navigateBack({
									delta: 1
								})
							}, 1000)
						} else {
							this.$toast('删除失败', 1000, 'none', true);
						}
					} else {
						this.$toast('出错了', 1000, 'none', true);
					}
				}).catch(err => {
					this.$toast(err, 1000, 'none', true);
				})}
			},
			saveEduExp:function(){
				if(this.id == 0){
					this.$post('resume/saveIntExp',{
						userId: wx.getStorageSync('userInfo').userId,
						companyName:this.companyName,
						postion:this.postion,
						startDate:this.startDate,
						endDate:this.endDate,
						description:this.description
					}).then(res => {
						console.log(res)
						if (res.code == 200) {
							if (res.result == 'success') {
								//更新缓存
								wx.setStorageSync('resume',res.data);
								this.$toast('保存成功', 1000, 'none', true);
								setTimeout(() => {
									uni.navigateBack({
										delta: 1
									})
								}, 1000)
							} else {
								this.$toast('保存失败', 1000, 'none', true);
							}
						} else {
							this.$toast('出错了', 1000, 'none', true);
						}
					}).catch(err => {
						this.$toast(err, 1000, 'none', true);
					})
				}else{
					this.$post('resume/updateIntExp',{
						id: this.id,
						companyName:this.companyName,
						postion:this.postion,
						startDate:this.startDate,
						endDate:this.endDate,
						description:this.description
					}).then(res => {
						console.log(res)
						if (res.code == 200) {
							if (res.result == 'success') {
								//更新缓存
								wx.setStorageSync('resume',res.data);
								this.$toast('更新成功', 1000, 'none', true);
								setTimeout(() => {
									uni.navigateBack({
										delta: 1
									})
								}, 1000)
							} else {
								this.$toast('更新失败', 1000, 'none', true);
							}
						} else {
							this.$toast('出错了', 1000, 'none', true);
						}
					}).catch(err => {
						this.$toast(err, 1000, 'none', true);
					})
				}
			},
			bindStartDateChange (e) {
			     this.startDate = e.detail.value
			},
			bindEndDateChange (e) {
			     this.endDate = e.detail.value
			}
		}
	}
</script>

<style lang="less">
	.inter_title {
		margin: 20rpx;
		font-size: 40rpx;
		font-weight: 600;
	}
	.title {
		font-size: 32rpx;
		padding-top: 10rpx;
		padding-left: 20rpx;
	}
	.inter_item {
		background-color: #FFFFFF;
		border-bottom: 1rpx solid #F1F1F1;
		input {
			padding: 10rpx 20rpx;
			width: 100%;
		}
		picker {
			padding: 10rpx 20rpx;
		}
		textarea{
			padding: 10rpx 20rpx;
		}

		.icon-xuanzeqixiayige {
			position: absolute;
			right: 20rpx;
			margin-top: -50rpx;
		}
	}


	.btn_remove {
		background-color: #55aaff;
		position: fixed;
		width: 40%;
		margin-left: 30rpx;
		bottom: 10rpx;
		color: #FFFFFF;
		font-size: 36rpx;
		
	}
	.btn_save {
		background-color: #55aaff;
		position: fixed;
		width: 40%;
		margin-left: 420rpx;
		bottom: 10rpx;
		color: #FFFFFF;
		font-size: 36rpx;
	}
</style>
