<template>
	<view class="attachmentResume_page">
		<view class="resume_wrap">
			<view class="wrap_top">
				<l-file ref="lFile" @up-success="onSuccess"></l-file>
				<view class="title">附件简历</view>
				<view class="iconfont icon-tianjia" @click="onUpload"></view>
			</view>
			<view class="reume_item" v-for="(item,index) in resumeList" :key="index">
				<image src="../../static/file.png" mode="widthFix"></image>
				<view class="resume_info" @click="openPdf(item.resumeUrl)">
					<view class="resume_name">{{ item.resumeName }}</view>
					<view class="resume_date">{{ item.publicDate }}</view>
				</view>
				<view class="iconfont icon-shanchuwenjian" @click="deleteResume(item.id)"></view>
			</view>
		</view>
	</view>

</template>
<script>
	import lFile from '@/components/l-file/l-file.vue'
	export default {
		components: {
			lFile
		},
		data() {
			return {
				resumeList: []
			}
		},
		onShow() {

			if (uni.getStorageSync('isLogin')) {
				this.loadResumeData();
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

		},
		methods: {
			deleteResume: function(id) {
				this.$post("attachmentResume/deleteResume", {
					id: id
				}).then(res => {
					if (res.code == 200) {
						if (res.result == 'success') {
							this.loadResumeData();
							this.$toast('删除成功', 1000, 'none', true);

						} else {
							this.$toast('删除失败', 1000, 'none', true);
						}
					} else {
						this.$toast('出错了', 1000, 'none', true);
					}
				}).catch(err => {
					this.$toast('出错了', 1000, 'none', true);
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

				this.$post('attachmentResume/selectResume', {
					userId: wx.getStorageSync('userInfo').userId
				}).then(res => {
					if (res.code == 200) {
						if (res.result == 'success') {
							this.resumeList = res.data;

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
			/* 上传 */
			onUpload() {
				this.$refs.lFile.upload({
					// #ifdef APP-PLUS
					currentWebview: this.$mp.page.$getAppWebview(),
					// #endif
					url: 'http://localhost:8080/upload/uploadFile',
					name: 'uploadFile',

				});
			},
			onSuccess(res) {
				console.log(res);
				uni.showToast({
					title: '上传成功',
					icon: 'none'
				})
				this.$post('attachmentResume/saveResume', {
					userId: wx.getStorageSync('userInfo').userId,
					resumeName: res.fileName,
					resumeUrl: res.data.data
				}).then(res => {
					if (res.code == 200) {
						if (res.result == 'success') {
							console.log(res)
							this.resumeList.push(res.data)
							this.$toast('保存成功', 1000, 'none', true);

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
		}

		.iconfont {
			position: absolute;
			right: 30rpx;
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
