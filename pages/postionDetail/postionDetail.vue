<template>
	<view class="postion_page">
		<view class="postion_info">
			<view class="postion_name">{{ postionData.postionName }}</view>
			<view class="demand">{{ postionData.demandEducation }}|{{ postionData.demandMajor}}|招收{{ postionData.num}}人</view>
			<view class="salary">{{ postionData.salary}}/月</view>

			<navigator :url="'../companyDetail/companyDetail?companyId=' + companyData.companyId" hover-class="none">
				<view class="company_info">
					<image :src="companyData.logo" mode="widthFix"></image>
					<view class="company_describe">
						<view class="company_name">{{ companyData.companyName }}</view>
						<view class="company_describe">{{ tradeData.tradeName}} {{ companyData.scale }}人</view>
					</view>
				</view>
			</navigator>

			<view class="postion_describe">
				<view class="title">职位描述</view>
				<view class="describe_content">
					<rich-text :nodes="postionData.description"></rich-text>
				</view>


			</view>

			<view class="work_place">
				<view class="title">工作地点</view>
				<view class="place_content">
					<text class="iconfont icon-position"></text>
					<view>{{ postionData.location}}</view>
				</view>
			</view>
		</view>
		<view class="btm_tool">
			<view class="tool_item">
				<view class="iconfont icon-fenxiang1"></view>
				<view>分享</view>
				<button open-type="share"></button>
			</view>
			<view v-if="isCollect == false" class="tool_item" @click="collectPostion">
				<view class="iconfont icon-shoucang1"></view>
				<view>收藏</view>
			</view>
			<view v-else-if="isCollect == true" class="tool_item" @click="deleteCollectPostion">
				<view class="iconfont icon-shoucang1"></view>
				<view>取消收藏</view>
			</view>
			<button v-if="isDeliver == false" class="tool_item btn_deliver" @click="deliver">
				投个简历
			</button>
			<button v-else-if="isDeliver == true"  class="tool_item btn_deliver" >
				已投递
			</button>
		</view>

		<switchEdit :direction="direction" @close="Close" @deliver="toDeliver" @toOnlineResume="toOnlineResume" 
					:Uptag="Uptag" 
					:bodyBtn="true"
					:resumeName="resume.resumeName"
					:resumeUrl="resume.resumeUrl"
					:userName="userInfo.userName">
		</switchEdit>


	</view>
</template>

<script>
	import switchEdit from '@/components/switchEdit.vue'
	export default {
		components: {
			switchEdit
		},
		data() {
			return {
				postionData: {},
				companyData: {},
				tradeData: {},
				isCollect: false,
				contentId: '',
				description: '',
				direction: 'bottom',
				//弹出标志
				Uptag: false,
				resume:{},
				userInfo:{},
				isDeliver:false
			}
		},
		onLoad(option) {

			this.loadPostionData(option.postionId);

		},
		methods: {
			deliver: function() {
				if (!wx.getStorageSync('isLogin')) {
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
				} else {
					this.Uptag = !this.Uptag;
					this.$post('attachmentResume/selectResume', {
						userId: this.userInfo.userId
					}).then(res => {
						if (res.code == 200) {
							if (res.result == 'success') {
								console.log(res.data)
								this.resume = res.data[0];
								console.log(this.resume)
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


			},
			//关闭按钮
			Close: function(data) {
				this.Uptag = data
			},
			toOnlineResume:function(){
				uni.navigateTo({
					url:'../onlineResume/onlineResume'
				})
			},
			toDeliver:function(data){
			
				var deliverId = this.userInfo.userId;
				var postionId = this.postionData.postionId;
				var publicerId = this.postionData.publicerId;
				var resumeId = 0;
				var type = 0;
				if(data == 1){
					resumeId = this.resume.id;
					type = 1;
				}
				this.$post('deliver/saveDeliver',{
					deliverId:deliverId,
					postionId:postionId,
					publicerId:publicerId,
					resumeId:resumeId,
					type:type
				}).then(res => {
					if (res.code == 200) {
						if (res.result == 'success') {
							console.log(res.data)
							this.isDeliver = true;
							this.$toast('投递成功', 1000, 'none', true);

						} else {
							this.$toast('投递失败', 1000, 'none', true);
						}
					} else {
						this.$toast('出错了', 1000, 'none', true);
					}
				}).catch(err => {
					this.$toast('出错了', 1000, 'none', true);
				})
			},
			loadPostionData: function(pId) {
				this.userInfo = uni.getStorageSync('userInfo')
				this.$post('postion/selectPostion', {
					postionId: pId,
				}).then(res => {
					if (res.code == 200) {
						if (res.result == 'success') {
							console.log(res.data)
							this.companyData = res.data[0];
							this.postionData = res.data[1];
							this.tradeData = res.data[2];
							this.description = this.postionData.description
							this.checkIsCollect();
							this.checkIsDeliver(this.userInfo.userId,this.postionData.postionId)
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
			deleteCollectPostion: function() {
				this.$post('collect/deleteCollect', {
					userId: this.userInfo.userId,
					contentId: this.postionData.postionId
				}).then(res => {
					if (res.code == 200) {
						if (res.result == 'success') {
							this.isCollect = false;
							this.$toast('取消收藏成功', 1000, 'none', true);

						} else {
							this.$toast('取消收藏失败', 1000, 'none', true);
						}
					} else {
						this.$toast('出错了', 1000, 'none', true);
					}
				}).catch(err => {
					this.$toast('出错了', 1000, 'none', true);
				})
			},
			checkIsDeliver:function(userId,postionId){
				
				this.$post('deliver/checkDeliver', {
					userId: userId,
					postionId: postionId
				}).then(res => {
					if (res.code == 200) {
						if (res.result == 'success') {
							console.log("res",res);
							this.isDeliver = true;
							
							this.$toast('查询成功', 1000, 'none', true);
				
						} else {
							this.$toast('查询失败', 1000, 'none', true);
						}
					} 
				}).catch(err => {
					
					this.$toast('出错了', 1000, 'none', true);
				})
				
			},
			checkIsCollect: function() {
				this.$post('collect/selectOneCollect', {
					userId: this.userInfo == '' ? 0 :  this.userInfo.userId,
					contentId: this.postionData.postionId
				}).then(res => {
					if (res.code == 200) {
						if (res.result == 'success') {
							console.log("check  " + res.data)
							if (res.data != null) {
								this.isCollect = true;
							}
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
			collectPostion: function() {
				if (!wx.getStorageSync('isLogin')) {
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
				} else {
					this.$post('collect/saveCollect', {
						userId: this.userInfo.userId,
						contentId: this.postionData.postionId,
						type: 0
					}).then(res => {
						if (res.code == 200) {
							if (res.result == 'success') {
								this.isCollect = true;
								this.$toast('收藏成功', 1000, 'none', true);

							} else {
								this.$toast('收藏失败', 1000, 'none', true);
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
	}
</script>

<style lang="less">
	.postion_info {
		.postion_name {
			color: #000000;
			font-size: 40rpx;
			font-weight: 500;
			margin: 20rpx;
		}

		.demand {
			color: #666;
			margin-left: 20rpx;
		}

		.salary {
			margin: 20rpx;
			color: #D1372C;
		}

		.company_info {
			display: flex;
			width: 90%;
			margin: 40rpx;

			-moz-box-shadow: 0 0 10px #f0f0f0;
			-webkit-box-shadow: 0 0 10px #f0f0f0;
			box-shadow: 0 0 10px #f0f0f0;

			image {
				margin: 30rpx;
				width: 20%;
			}

			.company_describe {

				.company_name {
					margin-top: 50rpx;

					font-size: 32rpx;
					color: #000000;
				}

				.company_describe {

					color: #666;
				}
			}
		}


		.postion_describe {
			.title {
				font-size: 36rpx;
				font-weight: 500;
				margin-left: 20rpx;
				margin-top: 40rpx;
			}

			.describe_content {
				width: 95%;
				margin: 20rpx;
				color: #666;
			}
		}

		.work_place {
			margin-bottom: 200rpx;

			.title {
				font-size: 36rpx;
				font-weight: 500;
				margin-left: 20rpx;
				margin-top: 40rpx;
			}

			.place_content {
				display: flex;
				color: #666;
				margin: 20rpx;
			}
		}
	}

	.btm_tool {
		position: fixed;
		left: 0;
		bottom: 0;
		width: 100%;
		height: 120rpx;
		background-color: #fff;
		display: flex;

		.tool_item {

			display: flex;
			flex-direction: column;
			justify-content: center;
			align-items: center;
			font-size: 24rpx;
			position: relative;
			width: 100rpx;

			button {
				position: absolute;
				top: 0;
				left: 0;
				width: 100%;
				height: 100%;
				opacity: 0;
			}
		}

		.btn_deliver {
			height: 90rpx;
			width: 500rpx;
			background-color: #52a4f6;
			color: #fff;
			font-size: 30rpx;
			font-weight: 600;
			border-radius: 50rpx;
			margin-left: 40rpx;
			margin-top: 20rpx;
		}
	}
</style>
