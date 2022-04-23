<template>
	<view class="recuiter_page">
		<view class="recuiter_wrap">
			<navigator class="recruit_item" :url="'../companyDetail/companyDetail?companyId=' + companyId ">
				<view class="title">我认证的公司</view>
				<view class="browse">查看更多</view>
				<view class="iconfont icon-xuanzeqixiayige"></view>
			</navigator>

			<navigator class="recruit_item">
				<view class="title">我发布的职位</view>
				<view class="browse" @click="browsePostionList">查看更多</view>
				<view class="iconfont icon-xuanzeqixiayige"></view>
			</navigator>

			<navigator class="recruit_item" url="resumeList/resumeList">
				<view class="title">我收到的简历</view>
				<view class="browse">查看更多</view>
				<view class="iconfont icon-xuanzeqixiayige"></view>
			</navigator>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				companyId: '',
				userId: ''
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
			}else{
				this.loadData();
			}
			
		},
		methods: {
			loadData: function() {
				this.$post('verify/selectVerify', {
					userId: uni.getStorageSync('userInfo').userId
				}).then(res => {
					if (res.code == 200) {
						if (res.result == 'success') {
							this.companyId = res.data.companyId;
							uni.setStorageSync('verify_companyId', this.companyId);
							console.log(res.data)
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
			browsePostionList: function() {
				uni.navigateTo({
					url: 'postionList/postionList'
				})
			}
		}
	}
</script>

<style lang="less">
	page {
		background-color: #f7f6fa;
	}



	.recruit_item {
		display: flex;

		padding: 30rpx;
		margin-top: 20rpx;
		width: 100%;
		background-color: #FFFFFF;

		.title {

			font-size: 32rpx;
			font-weight: 600;
		}

		.browse {
			position: absolute;
			right: 50rpx;

		}

		.iconfont {
			position: absolute;
			right: 20rpx;

		}

	}
</style>
