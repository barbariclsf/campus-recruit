<template>
	<view class="verify_page">
		<view class="verify_wrap">
			<view class="verify_item">
				<view class="title">公司名称</view>
				<picker @change="bindCompanyPickerChange" :range="companyArrays">
					<label>{{companyArrays[companyIndex]}}</label>
				</picker>
				<view class="iconfont icon-xuanzeqixiayige"></view>
			</view>


			<view class="verify_item">
				<view class="title">部门</view>
				<input type="text" v-model="department" />
			</view>

			<view class="verify_item">
				<view class="title">姓名</view>
				<input type="text" v-model="userName" />
			</view>

			<view class="verify_item">
				<view class="title">工号</view>
				<input type="text" v-model="jobNumber" />
			</view>
			<button class="verify_btn" @click="submitVerify">提交</button>
		</view>
	</view>
</template>

<script>
	

	export default {
		
		data() {
			return {
				companyList: [],
				companyArrays: [],
				companyIndex: 0,
				companyName: '',
				department: '研发部',
				userName: '',
				jobNumber: '',
				companyId: 0

			}
		},
		onShow() {
			if (uni.getStorageSync('userInfo') == '') {
				uni.showModal({
					title: '登录提示',
					content: '您还未登录, 请先登录',
					showCancel: false,
					success: (res) => {
						if (res.confirm) {
							uni.switchTab({
								url: '../user/user'
							})
						}
					}
				})
			}
			this.loadCompanyData();
			
		},
		methods: {
			submitVerify: function() {
				this.$post('verify/saveVerify', {
					companyId: this.companyId,
					department: this.department,
					userId: wx.getStorageSync('userInfo').userId,
					userName: this.userName,
					jobNumber: this.jobNumber
				}).then(res => {
					if (res.code == 200) {
						if (res.result == 'success') {
							console.log(res);
							setTimeout(() => {
								uni.navigateBack({
									delta: 1
								})
							}, 1000)
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
			loadCompanyData: function() {

				this.$get('company')
					.then(res => {
						if (res.code == 200) {
							if (res.result == 'success') {
								console.log(res);
								 
								
								this.companyArrays = [];
								res.data.forEach(v => {
									this.companyList.push(v.company);
									this.companyArrays.push(v.company.companyName);
								})
								this.companyName = this.companyArrays[this.companyId];
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
			bindCompanyPickerChange: function(e) {
				this.companyIndex = e.detail.value;
				this.companyName = this.companyArrays[this.companyIndex];
				this.companyList.forEach(v => {
					if(v.companyName == this.companyName){
						this.companyId = v.companyId
					}
				})
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
	}

	.verify_btn {
		position: fixed;
		margin-left: 20rpx;
		bottom: 20rpx;
		width: 95%;
		background-color: #4e9dec;
		color: #FFFFFF;
	}
</style>
