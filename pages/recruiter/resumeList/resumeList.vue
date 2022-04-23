<template>
	<view class="attachmentResume_page">
		<view class="resume_wrap">
			<view class="wrap_top">
				<view class="title">我收到的简历</view>
				<view class="resume_num">共{{ deliverList.length }}份</view>
			</view>
			<view class="reume_item" v-for="(item,index) in deliverList" :key="index">
				<image src="../../../static/file.png" mode="widthFix"></image>
				<view v-if="item.type == 1" class="resume_info" @click="openPdf(item.resumeUrl)">
					<view class="resume_name">{{ item.resumeName }}</view>
					<view class="resume_date">{{ item.deliverDate }}</view>
				</view>
				<view v-if="item.type == 0" class="resume_info" @click="toOnlineResume(item.deliverId)">
					<view class="resume_name">{{ item.deliverName }}在线简历</view>
					<view class="resume_date">{{ item.deliverDate }}</view>
				</view>
			</view>
		</view>
	</view>

</template>
<script>
	export default {
		data() {
			return {
				deliverList: []
			}
		},
		onShow() {
			this.loadResumeData();
		},
		methods: {
			
			toOnlineResume:function(uid){
				uni.navigateTo({
					url:'../../onlineResume/onlineResume?uid=' + uid + '&type=1'
				})
			},
			openPdf: function(pdfUrl) {
			
				uni.showLoading({
					title: '正在打开...'
				})
				uni.downloadFile({
					url: pdfUrl,
					success: function(res) {
						console.log('下载的res', res)
						var filePath = res.tempFilePath
						uni.openDocument({
							filePath: encodeURI(filePath),
							fileType: 'pdf',
							success: function(res) {
								uni.hideLoading()
								uni.showToast({
								  title: '打开文档成功',
								  duration: 1000,
								  icon: 'none'
								})
								
							},
							fail: function(err) {
								uni.hideLoading()
								uni.showToast({
									title: '打开失败',
									duration: 1500,
									icon: 'none'
								})
								console.log('打开失败')
							}
						})
					},
					fail: function(err) {
						console.log('下载失败原因', err)
						uni.hideLoading()
						setTimeout(() => {
							uni.showToast({
								title: '下载失败',
								duration: 1500,
								icon: 'none'
							})
						}, 1500)
					}
				})
			},
			loadResumeData: function() {
				this.$post('deliver/selectByPubId', {
					publicerId: wx.getStorageSync('userInfo').userId
				}).then(res => {
					if (res.code == 200) {
						if (res.result == 'success') {
							this.deliverList = res.data;

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
		}
	}
</script>

<style lang="less">
	.wrap_top {
		display: flex;
		margin-top: 10rpx;
		margin-left: 30rpx;

		.title {
			font-size: 36rpx;
			font-weight: 600;
		}

		.iconfont {
			position: absolute;
			right: 30rpx;
		}
		.resume_num{
			margin-top: 10rpx;
			margin-left: 20rpx;
			color: #666;
		}
	}

	.reume_item {
		display: flex;
		width: 95%;
		margin: 20rpx;
		-moz-box-shadow: 0 0 10px #f0f0f0;
		-webkit-box-shadow: 0 0 10px #f0f0f0;
		box-shadow: 0 0 10px #f0f0f0;

		image {
			width: 20%;
		}

		.resume_info {
			display: flex;
			flex-direction: column;
			margin-top: 40rpx;
			margin-left: 20rpx;
			font-size: 36rpx;
		}

		.resume_name {

			width: 320rpx;
			height: 40rpx;
			overflow: hidden;
			text-overflow: ellipsis;
			white-space: nowrap;

		}

		.resume_date {
			font-size: 28rpx;
			color: #666;
			margin-top: 10rpx;
		}

		.iconfont {
			position: absolute;
			margin-top: 50rpx;
			right: 60rpx;

		}

	}
</style>
