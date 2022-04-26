<template>
	<view class="user_detail_page">
		<view class="user_info">
			<view class="user_item" style="height: 150rpx;">
				<view class="title">头像</view>
				<view style="color: #666;margin: 20rpx;">上传证件照，通过hr初筛率更高</view>
				<image :src="avatar" mode="widthFix"></image>
			</view>

			<view class="user_item">
				<view class="title">姓名</view>
				<input type="text" v-model="userName" />
			</view>

			<view class="user_item">
				<view class="title">出生日期</view>
				<picker mode="date" :value="birthday" start="1970-01-01" @change="bindDateChange">
					<label>{{birthday}}</label>
				</picker>
				<view class="iconfont icon-xuanzeqixiayige"></view>
			</view>


			<view class="user_item">
				<view class="title">性别</view>
				<picker @change="bindSexPickerChange" :range="sexArrays">
					<label>{{sexArrays[sexIndex]}}</label>
				</picker>
				<view class="iconfont icon-xuanzeqixiayige"></view>
			</view>

			<view class="user_item">
				<view class="title">联系邮箱</view>
				<input type="text" v-model="email" />
			</view>

			<view class="user_item">
				<view class="title">联系电话</view>
				<input type="text" v-model="telephone" />
			</view>

			<view class="user_item">
				<view class="title">生源地</view>
				<input type="text" v-model="address" />
			</view>
		</view>


		<view class="school_info">
			<view class="school_item">
				<view class="title">最高学历</view>
				<picker @change="bindEducationPickerChange" :range="educationArrays">
					<label>{{educationArrays[educationIndex]}}</label>
				</picker>
				<view class="iconfont icon-xuanzeqixiayige"></view>
			</view>

			<view class="school_item">
				<view class="title">最高学历的学校</view>
				<input type="text" v-model="schoolName" />
			</view>

			<view class="school_item">
				<view class="title">最高学历的专业</view>
				<input type="text" v-model="major" />
			</view>

			<view class="school_item">
				<view class="title">毕业年份</view>
				<picker @change="bindGraduationPickerChange" :range="graduationArrays">
					<label>{{graduationArrays[graduationIndex]}}</label>
				</picker>
				<view class="iconfont icon-xuanzeqixiayige"></view>
			</view>
		</view>
		<button class="btn_save" @click="saveUserInfo">保存</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				avatar: '',
				sexArrays: ['男', '女'],
				sexIndex: 0,
				educationArrays: ['博士', '硕士', '本科', '大专', '高中', '中专', '初中及一下'],
				educationIndex: 0,
				graduationArrays: ['2023', '2022', '2021', '2020', '2019', '2019'],
				graduationIndex: 0,
				birthday: '2021-01-01',
				userName: '',
				email: 'xxx@qq.com',
				telephone: '159xxxxxxxx',
				address: '',
				schoolName: '',
				major: '',
				highestDegree: '',
				graduationYear: ''
			}
		},
		onShow() {
			let userInfo = wx.getStorageSync('userInfo');
			if (userInfo != null) {
				this.userName = userInfo.userName;
				this.avatar = userInfo.avatar;
				this.birthday = userInfo.birth;
				this.sexIndex = userInfo.sex;
				this.email = userInfo.email;
				this.telephone = userInfo.telephone;
				this.address = userInfo.address;
				var educationIndex = this.educationArrays.findIndex(v => v == userInfo.highestDegree);
				if (educationIndex == -1) educationIndex = 0;
				this.educationIndex = educationIndex;
				this.highestDegree = this.educationArrays[educationIndex]
				this.schoolName = userInfo.schoolName;
				this.major = userInfo.major;
				var graduationIndex = this.graduationArrays.findIndex(v => v == userInfo.graduationYear);
				if (graduationIndex == -1) graduationIndex = 0;
				this.graduationIndex = graduationIndex;
				this.graduationYear = this.graduationArrays[graduationIndex];


			} else {
				this.loadJobIntentionData();
			}
		},
		onPullDownRefresh() {},
		methods: {

			saveUserInfo: function() {
				var userInfo = wx.getStorageSync("userInfo");
				if (userInfo != null) {
					this.$post('user/updateUserInfo', {
						userId: userInfo.userId,
						userName: this.userName,
						birth: this.birthday,
						sex: this.sexIndex,
						email: this.email,
						telephone: this.telephone,
						address: this.address,
						highestDegree: this.highestDegree,
						schoolName: this.schoolName,
						major: this.major,
						graduationYear: this.graduationYear
					}).then(res => {
						if (res.code == 200) {
							if (res.result == 'success') {
								console.log(res);
								wx.setStorageSync('userInfo', res.data);
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
						this.$toast('出错了', 1000, 'none', true);
					})
				}
			},
			loadJobIntentionData: function() {
				this.$post('resume/selectJobIntention', {
					userId: wx.getStorageSync("userInfo").userId
				}).then(res => {
					if (res.code == 200) {
						if (res.result == 'success') {
							console.log(res);
							this.jobIntention = res.data;
							wx.setStorageSync('jobIntention', this.jobIntention);
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
			bindSexPickerChange(e) {
				this.sexIndex = e.detail.value;
				this.sex = this.sexArrays[this.sexIndex];

			},
			bindEducationPickerChange(e) {
				this.educationIndex = e.detail.value;
				this.highestDegree = this.educationArrays[this.educationIndex];
			},
			bindGraduationPickerChange(e) {
				this.graduationIndex = e.detail.value;
				this.graduationYear = this.graduationArrays[this.graduationIndex];
			},
			bindDateChange(e) {
				this.birthday = e.detail.value
			},

		}
	}
</script>

<style lang="less">
	page {
		background-color: #f0f0f0;
	}

	.user_item {
		background-color: #FFFFFF;

		border-bottom: 1rpx solid #F1F1F1;

		image {
			width: 15%;
			position: absolute;
			right: 20rpx;
			top: 20rpx;
			border-radius: 50%;
		}

		.title {
			font-size: 32rpx;
			padding-top: 20rpx;
			padding-left: 20rpx;
		}

		input {
			padding: 20rpx;
			width: 100%;
		}

		picker {
			padding: 20rpx;

		}

		.icon-xuanzeqixiayige {
			position: absolute;
			right: 20rpx;
			margin-top: -50rpx;
		}
	}

	.school_info {
		margin-top: 20rpx;
		margin-bottom: 100rpx;

		.school_item {
			background-color: #FFFFFF;
			border-bottom: 1rpx solid #F1F1F1;

			.title {
				font-size: 32rpx;
				padding-top: 20rpx;
				padding-left: 20rpx;
			}

			input {
				padding: 20rpx;
				width: 100%;
			}

			picker {
				padding: 20rpx;

			}

			.icon-xuanzeqixiayige {
				position: absolute;
				right: 20rpx;
				margin-top: -50rpx;
			}
		}
	}

	.btn_save {
		background-color: #55aaff;
		position: fixed;
		width: 95%;
		margin-left: 20rpx;
		bottom: 10rpx;
		color: #FFFFFF;
		font-size: 36rpx;
	}
</style>
