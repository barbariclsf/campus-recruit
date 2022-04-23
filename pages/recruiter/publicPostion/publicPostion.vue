<template>
	<view class="verify_page">
		<view class="verify_wrap">
			<view class="verify_item">
				<view class="title">公司名称</view>
				<view class="item_name" v-model="company"> {{companyName}} </view>
			</view>


			<view class="verify_item">
				<view class="title">职位名称</view>
				<input type="text" v-model="postionName" />
			</view>

			<view class="verify_item">
				<view class="title">薪资</view>
				<picker @change="bindSalaryPickerChange" :range="salaryArrays">
					<label>{{salaryArrays[salaryIndex]}}</label>
				</picker>
				<view class="iconfont icon-xuanzeqixiayige"></view>
			</view>
			
			<view class="verify_item">
				<view class="title">学历要求</view>
				<picker @change="bindEducationPickerChange" :range="educationArrays">
					<label>{{educationArrays[educationIndex]}}</label>
				</picker>
				<view class="iconfont icon-xuanzeqixiayige"></view>
			</view>
			
			<view class="verify_item">
				<view class="title">专业要求</view>
				<input type="text" v-model="demandMajor" />
			</view>
			
			<view class="verify_item">
				<view class="title">招聘人数</view>
				<input type="text" v-model="num" />
			</view>
			
			<view class="verify_item">
				<view class="title">工作地点</view>
				<input type="text" v-model="location" />
			</view>
			<view class="verify_item">
				<view class="title">职位描述</view>
				<rich-text-editor v-model="description"></rich-text-editor>
			</view>
		</view>
		<button class="submit_btn" v-show="!isAlter" @click="submitpublic">提交</button>
		<button class="save_btn" v-show="isAlter" @click="savepublic">保存</button>
	</view>
</template>

<script>
	import RichTextEditor from "@/components/RichTextEditor.vue"

	export default {
		components: {
			RichTextEditor,

		},
		data() {
			return {
				postionId:'',
				salaryArrays: ['2000-5000', '5000-8000', '8000-1500', '15000以上'],
				salaryIndex: 0,
				educationArrays: ['博士', '硕士', '本科', '大专', '高中', '中专', '初中及一下'],
				educationIndex: 0,
				companyId:'',
				companyName:'',
				salary:'2000-5000',
				demandEducation:'博士',
				demandMajor:'不限',
				location:'北京',
				description:'',
				postionName:'',
				num:1,
				isAlter:false
				
			}
		},
		onLoad(option){
			if(option.postionId != 0){
				//修改
				console.log(option)
				this.isAlter = true;
				var postionDetail = wx.getStorageSync('postionDetail');
				var company = postionDetail.company;
				var postion = postionDetail.postion;
				this.companyName = company.companyName;
				this.companyId = company.companyId;
				this.postionId = postion.postionId;
				this.postionName = postion.postionName;
				this.salary = postion.salary;
				this.num = postion.num;
				this.salaryArrays.forEach((v,i) => {
					if(v == this.salary){
						this.salaryIndex = i;
					}
				})
				this.demandEducation = postion.demandEducation;
				this.educationArrays.forEach((v,i) => {
					if(v == this.demandEducation){
						this.educationIndex = i;
					}
				})
				this.demandMajor = postion.demandMajor;
				this.location = postion.location;
				this.description = postion.description;
				
				
			}else{
				this.companyId = uni.getStorageSync('verify_companyId');
				this.loadDate();
			}
		
		},
		methods: {
			savepublic:function(){
				this.$post('postion/updatePostion',{
					postionId:this.postionId,
					postionName:this.postionName,
					salary:this.salary,
					demandEducation:this.demandEducation,
					demandMajor:this.demandMajor,
					num:this.num,
					location:this.location,
					description:this.description
				}).then(res => {
						if (res.code == 200) {
							if (res.result == 'success') {
								console.log(res);
								uni.navigateTo({
									url:'../postionList/postionList'
								})
								this.$toast('查询成功', 1000, 'none', true);
				
							} else {
								this.$toast('查询失败', 1000, 'none', true);
							}
						} else {
							this.$toast('出错了', 1000, 'none', true);
						}
					}).catch(err => {
						this.$toast('出错了', 1000, 'none', true);
					})
			},
			submitpublic:function(){
				this.$post('postion/savePostion',{
					companyId:this.companyId,
					publicerId:uni.getStorageSync('userInfo').userId,
					postionName:this.postionName,
					salary:this.salary,
					num:this.num,
					demandEducation:this.demandEducation,
					demandMajor:this.demandMajor,
					location:this.location,
					description:this.description
				}).then(res => {
						if (res.code == 200) {
							if (res.result == 'success') {
								console.log(res);
								uni.navigateTo({
									url:'../postionList/postionList'
								})
								this.$toast('查询成功', 1000, 'none', true);
				
							} else {
								this.$toast('查询失败', 1000, 'none', true);
							}
						} else {
							this.$toast('出错了', 1000, 'none', true);
						}
					}).catch(err => {
						this.$toast('出错了', 1000, 'none', true);
					})
			},
			
			loadDate:function(){
				this.$post('company/selectCompany',{
					companyId: this.companyId
				}).then(res => {
						if (res.code == 200) {
							if (res.result == 'success') {
								console.log(res);
								 this.isAlter = false;
								this.companyName = res.data[0].companyName;
							
								this.$toast('查询成功', 1000, 'none', true);
				
							} else {
								this.$toast('查询失败', 1000, 'none', true);
							}
						} else {
							this.$toast('出错了', 1000, 'none', true);
						}
					}).catch(err => {
						this.$toast('出错了', 1000, 'none', true);
					})
			
			},
			bindEducationPickerChange:function(e) {
				this.educationIndex = e.detail.value;
				this.demandEducation = this.educationArrays[this.educationIndex];
			},
			bindSalaryPickerChange:function(e) {
				this.salaryIndex = e.detail.value;
				this.salary = this.salaryArrays[this.salaryIndex];
			}
			
			
		}
	}
</script>

<style lang="less">
	.verify_item {
		display: flex;
		flex-direction: row;
		padding: 20rpx;
		border-bottom: 1rpx solid #eaeaea;
	
		.title {
			font-size: 32rpx;
			padding: 10rpx;
		}
	
		picker {
			position: absolute;
			right: 60rpx;
			margin-top: 15rpx;
		}
		.item_name{
			position: absolute;
			right: 20rpx;
			margin-top: 10rpx;
		}
		.iconfont {
			position: absolute;
			right: 20rpx;
			margin-top: 10rpx;
		}
	
		input {
			position: absolute;
			right: 20rpx;
			text-align: right;
			color: #666;
		}
		rich-text-editor{
			margin-top: 55rpx;
			
		}
	}
	
	.submit_btn {
		position: fixed;
		margin-left: 20rpx;
		bottom: 20rpx;
		width: 95%;
		height: 80rpx;
		border-radius: 20rpx;
		background-color: #4e9dec;
		color: #FFFFFF;
		display: flex;
		justify-content: center;
		align-items: center;
	}
	.save_btn {
		position: fixed;
		margin-left: 20rpx;
		bottom: 20rpx;
		width: 95%;
		height: 80rpx;
		border-radius: 20rpx;
		background-color: #4e9dec;
		color: #FFFFFF;
		display: flex;
		justify-content: center;
		align-items: center;
	}
</style>
