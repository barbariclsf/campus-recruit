<template>
	<view class="experience_page">
		<view class="exp_wrap">
			<view class="exp_title">管理教育经历</view>
			<view class="exp_item">
				<view class="title">学校</view>
				<input type="text" v-model="schoolName" />
			</view>
			<view class="exp_item">
				<view class="title">专业</view>
				<input type="text" v-model="major" />
			</view>
			<view class="exp_item">
				<view class="title">学位</view>
				<picker @change="bindEducationPickerChange" :range="educationArrays">
					<label>{{educationArrays[educationIndex]}}</label>
				</picker>
				<view class="iconfont icon-xuanzeqixiayige">

				</view>
			</view>
			<view class="exp_item">
				<view class="title">开始时间</view>
				<picker mode="date" :value="startDate" start="1970-01-01" @change="bindStartDateChange">
					<label>{{startDate}}</label>
				</picker>
				<view class="iconfont icon-xuanzeqixiayige"></view>
			</view>

			<view class="exp_item">
				<view class="title">结束时间</view>
				<picker mode="date" :value="endDate" start="1970-01-01" @change="bindEndDateChange">
					<label>{{endDate}}</label>
				</picker>
				<view class="iconfont icon-xuanzeqixiayige"></view>
			</view>

			<view class="">
				<view class="title">在校经历</view>
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
				educationArrays: ['博士', '硕士', '本科', '大专', '高中', '中专', '初中及一下'],
				educationIndex: 0,
				startDate:'2021-01-01',
				endDate: '2021-01-01',
				eduExp:{},
				schoolName:'',
				major:'',
				degree:'本科',
				description:'',
				id:0
			}
		},
		onLoad(option){
			this.id = option.id;	
			if(this.id != 0){
				let edu = wx.getStorageSync('resume').eduExps;
				
				var index = edu.findIndex(v => v.id == this.id);
				this.schoolName = edu[index].schoolName;
				this.degree = edu[index].degree;
				this.educationIndex = this.educationArrays.findIndex(v => v == this.degree);
			
				this.major = edu[index].major;
				this.startDate = edu[index].startDate;
				this.endDate = edu[index].endDate;
				this.description = edu[index].description;
			}
		},
		
		methods: {
			deleteEduExp:function(){
				console.log(this.id)
				if(this.id == 0){
					this.$toast('先保存才能删除哦', 1000, 'none', true);
				}else{
					this.$post('resume/deleteEduExp',{
						id:this.id
					}).then(res => {
					console.log(res)
					if (res.code == 200) {
						if (res.result == 'success') {
							//更新缓存
							wx.setStorageSync('resume','');
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
					this.$post('resume/saveEduExp',{
						userId: wx.getStorageSync('userInfo').userId,
						schoolName:this.schoolName,
						degree:this.degree,
						major:this.major,
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
					this.$post('resume/updateEduExp',{
						id: this.id,
						schoolName:this.schoolName,
						degree:this.degree,
						major:this.major,
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
			bindEducationPickerChange(e){
				this.educationIndex = e.detail.value;
				this.degree = this.educationArrays[this.educationIndex];
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
	.exp_title {
		margin: 20rpx;
		font-size: 40rpx;
		font-weight: 600;
	}

	.exp_item {
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
	.title {
		font-size: 32rpx;
		padding-top: 10rpx;
		padding-left: 20rpx;
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
