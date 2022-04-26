<template>
	<view class="onlineResume_page">
		<view class="user_wrap">
			<image :src="userInfo.avatar" mode="widthFix"></image>
			<view class="user_info">
				<view class="user_name">{{ userInfo.userName }}</view>
				<view class="school_info">{{ userInfo.schoolName }}/{{ userInfo.highestDegree }}/{{ userInfo.major }}</view>
				<navigator v-if="isUserSelf" url="../user/userDetail/userDetail">
					<view class="iconfont icon-shuxie"></view>
				</navigator>
			</view>
		</view>
		<view class="resume_wrap">
			<view class="resume_item">
				<view class="title">求职意向</view>
				<navigator v-if="isUserSelf"  url="jobIntention/jobIntention">
					<view class="iconfont icon-shuxie"></view>
				</navigator>
				<view class="content" v-show="resume.jobIntention != null">
					<navigator  :url=" isUserSelf == false ? '' : jobIntention/jobIntention" hover-class="none">
						<view class="item">
							<view class="iconfont icon-hangye1"></view>
							<view class="item_name">{{ resume.jobIntention.trade }}</view>
						</view>
						<view class="item">
							<view class="iconfont icon-qiuzhizhuangtai"></view>
							<view class="item_name">{{ resume.jobIntention.postion }}</view>
						</view>
						<view class="item">
							<view class="iconfont icon-daifukuan"></view>
							<view class="item_name">{{ resume.jobIntention.salary }}</view>
						</view>

						<view class="item">
							<view class="iconfont icon-position"></view>
							<view class="item_name">{{ resume.jobIntention.location }}</view>
						</view>
					</navigator>
				</view>


			</view>
			<view class="resume_item">
				<view class="title">教育经历</view>
				<navigator v-if="isUserSelf" url="educationExperience/educationExperience?id=0">
					<view class="iconfont icon-tianjia"></view>
				</navigator>
				<view class="content">
					<view class="info" v-for="(item,index) in resume.eduExps" :key="index">
						<navigator :url=" isUserSelf == false ? '' : 'educationExperience/educationExperience?id='+item.id" hover-class="none">
							<view class="info_title">{{ item.schoolName }}</view>
							<view class="time">{{ item.startDate }}-{{ item.endDate }}</view>
							<view class="iconfont icon-xuanzeqixiayige"></view>
							<view class="info_content">{{ item.degree }}/{{ item.major }}</view>
							<rich-text  v-if="!isUserSelf" :nodes="item.description"></rich-text>
						</navigator>
					</view>

				</view>
			</view>


			<view class="resume_item">
				<view class="title">实习经历</view>
				<navigator v-if="isUserSelf" url="internshipExperience/internshipExperience?id=0">
					<view class="iconfont icon-tianjia"></view>
				</navigator>
				<view class="content">
					<view class="info" v-for="(item,index) in resume.intExps" :key="index">
						<navigator :url=" isUserSelf == false ? '' : 'internshipExperience/internshipExperience?id='+item.id" hover-class="none">
							<view class="info_title">{{ item.companyName }}</view>
							<view class="time">{{ item.startDate }}-{{ item.endDate }}</view>
							<view class="iconfont icon-xuanzeqixiayige"></view>
							<view class="info_content">{{ item.postion }}</view>
							<rich-text  v-if="!isUserSelf" :nodes="item.description"></rich-text>
						</navigator>
					</view>

				</view>
			</view>


			<view class="resume_item">
				<view class="title">项目经历</view>
				<navigator v-if="isUserSelf" url="projectExperience/projectExperience?id=0">
					<view class="iconfont icon-tianjia"></view>
				</navigator>
				<view class="content">
					<view class="info" v-for="(item,index) in resume.proExps" :key="index">
						<navigator :url=" isUserSelf == false ? '' : 'projectExperience/projectExperience?id='+item.id" hover-class="none">
							<view class="info_title">{{ item.projectName }}</view>
							<view class="time">{{ item.startDate }}-{{ item.endDate }}</view>
							<view class="iconfont icon-xuanzeqixiayige"></view>
							<view class="info_content">{{ item.postion }}</view>
							<rich-text  v-if="!isUserSelf" :nodes="item.description"></rich-text>
						</navigator>
					</view>
				</view>
			</view>


			<view class="resume_item">
				<view class="title">专业技能</view>
				<navigator v-if="isUserSelf"  url="professionalSkills/professionalSkills?id=0">
					<view class="iconfont icon-tianjia"></view>
				</navigator>
				<view class="content">
					<view class="info" v-for="(item,index) in resume.proSkills" :key="index">
						<navigator :url=" isUserSelf == false ? '' : 'professionalSkills/professionalSkills?id='+item.id" hover-class="none">
							<view class="info_title">{{ item.skillName }}</view>
							<view class="iconfont icon-xuanzeqixiayige"></view>
							<rich-text  v-if="!isUserSelf" :nodes="item.description"></rich-text>
						</navigator>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {

		data() {
			return {

				userInfo: {},
				resume: {},
				isUserSelf: true
			}
		},
		onPullDownRefresh() {
			setTimeout(()=>{
				this.userInfo = wx.getStorageSync('userInfo');
				this.loadResumeData(this.userInfo.userId);
				uni.stopPullDownRefresh();
			},1000)
			
		},
		onLoad(option) {
			var type = option.type;
			console.log(type)
			if(type == 1){
				
				var uid = option.uid;
				//简历被查看
				this.isUserSelf = false;
				this.$post('user/selectByUserId',{
					userId:uid
				}).then(res => {
					if (res.code == 200) {
						if (res.result == 'success') {
							this.userInfo = res.data;
							
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
				this.loadResumeData(uid);
			}else{
				//自己查看自己
				if (wx.getStorageSync('userInfo') != '') {
					this.userInfo = wx.getStorageSync('userInfo');
					this.loadResumeData(this.userInfo.userId);
				} else {
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
				
			}
			
		},
		methods: {

			loadResumeData: function(uid) {
				this.$post('resume', {
					userId: uid
				}).then(res => {
					if (res.code == 200) {
						if (res.result == 'success') {
							console.log(res);
							this.resume = res.data;
							uni.setStorageSync('resume',this.resume)
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


			}
		}
	}
</script>

<style lang="less">
	page {
		background-color: #f0f0f0;
	}

	.user_wrap {
		display: flex;
		background-color: #FFFFFF;
		border-bottom: 1rpx solid #f0f0f0;

		image {
			width: 20%;
			border-radius: 50%;
			margin: 30rpx;
		}

		.user_info {
			flex-direction: column;
			padding: 40rpx 20rpx;

			.user_name {
				font-size: 36rpx;
				font-weight: 600;
				margin-bottom: 20rpx;
			}

			.school_info {
				color: #666;
			}

			.iconfont {
				position: absolute;
				right: 20rpx;
				top: 30rpx;
			}
		}
	}

	.resume_item {
		display: flex;
		flex-direction: column;
		margin: 20rpx 0;
		background-color: #FFFFFF;
		color: #666;

		.icon-tianjia {
			position: absolute;
			right: 20rpx;
			margin-top: -60rpx;
		}

		.icon-shuxie {
			position: absolute;
			right: 20rpx;
			margin-top: -60rpx;
		}

		.content {
			border-top: 1rpx solid #EEEEEE;

			.item {
				display: flex;
				flex-direction: row;
				margin: 20rpx;

				.item_name {
					margin-left: 20rpx;
				}
			}

			.info {
				margin: 20rpx;

				.info_title {
					font-size: 32rpx;
					color: #333333;
					margin-bottom: 10rpx;
				}

				.time {
					position: absolute;
					right: 50rpx;
					margin-top: -40rpx;
				}

				.icon-xuanzeqixiayige {
					position: absolute;
					right: 20rpx;
					margin-top: -40rpx;
				}
			}

		}

		.title {
			margin: 20rpx;
			font-size: 36rpx;
			color: #333333;
		}


	}
</style>
