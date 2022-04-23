<template>
	<view class="company_page">
		<view class="company_info">
			<image :src="companyData.logo" mode="widthFix"></image>
			<view class="company_describe">
				<view class="company_name">{{ companyData.companyName }}</view>
				<view class="company_describe">
					{{ tradeData.tradeName}} {{ companyData.scale}}人
				</view>
			</view>
		</view>
		<tabs :tabs="tabs" @tabItemChange="handleTabItemChange">
			<block v-if="tabs[0].isActive">
				<view class="company_introduce">{{ companyData.description}}</view>
				<view class="work_place">
					<view class="title">地点</view>
					<view class="place_content">
						<view class="iconfont icon-position"></view>
						<view>{{ companyData.location}}</view>
					</view>
				</view>
			</block>
			<block v-else-if="tabs[1].isActive">
				<view class="recruit_info">
					<view class="title">校招职位</view>
					<view class="recruit_num">
						<view class="iconfont icon-zaizhi"></view>
						<text>目前在招职位 {{ companyData.postionNum }}</text>
					</view>
					<view class="postion_item">
						<navigator v-for="(item,index) in postionList" :key="index" :url="'../postionDetail/postionDetail?postionId=' + item.postionId"
						 hover-class="none">
							<view class="postion_item">
								<view class="postion_name">{{item.postionName}}</view>
								<view class="salary">{{ item.salary }}/月</view>
								<view class="demand">应届生|{{ item.demandEducation}}|{{ item.demandMajor }}</view>
								<view class="public_time">{{ item.publicDate }}</view>
							</view> 
						</navigator>
					</view>
				</view>
			</block>
		</tabs>
		<view class="btm_tool">
			<view class="tool_item share">
				<view class="iconfont icon-fenxiang1"></view>
				<view>分享</view>
				<button open-type="share"></button>
			</view>
			
			<view v-if="isCollect == false" class="tool_item collect" @click="collectCompany">
				<view class="iconfont icon-shoucang1"></view>
				<view>收藏</view>
			</view>
			<view v-else-if="isCollect == true" class="tool_item collect" @click="deleteCollectCompany">
				<view class="iconfont icon-shoucang1"></view>
				<view>取消收藏</view>
			</view>

		</view>
	</view>
</template>

<script>
	import tabs from '@/components/tabs.vue'
	export default {
		components: {
			tabs
		},
		data() {
			return {
				companyData: {},
				tradeData:{},
				postionList: [],
				isCollect: false,
				tabs: [{
						id: 0,
						value: "公司介绍",
						isActive: true
					},
					{
						id: 1,
						value: "校招职位",
						isActive: false
					},

				]
			}
		},
		onLoad(option) {
			
			this.loadPostionListData(option.companyId);
			
			
		},
		methods: {

			loadPostionListData: function(cId) {

				this.$post('company/selectCompany',{
					companyId: cId
				}).then(res => {
						if (res.code == 200) {
							if (res.result == 'success') {
								console.log( res.data);
								this.companyData = res.data[0];
								this.postionList = res.data[1];
								this.tradeData = res.data[2];
								this.checkIsCollect();
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
			checkIsCollect: function() {
				console.log(this.companyData.companyId)
				this.$post('collect/selectOneCollect', {
					userId: wx.getStorageSync('userInfo') == '' ? 0 : wx.getStorageSync('userInfo').userId,
					contentId: this.companyData.companyId
				}).then(res => {
					if (res.code == 200) {
						if (res.result == 'success') {
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
			deleteCollectCompany: function() {
				this.$post('collect/deleteCollect', {
					userId: wx.getStorageSync('userInfo').userId,
					contentId: this.companyData.companyId
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
			handleTabItemChange(index) {
				console.log(index)
				let oldTabs = this.tabs;
				oldTabs.forEach((v, i) => i == index ? v.isActive = true : v.isActive = false);
				this.tabs = oldTabs;
			},
			collectCompany: function() {
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
						userId: wx.getStorageSync('userInfo').userId,
						contentId: this.companyData.companyId,
						type: 1
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
			padding-bottom: 20rpx;
			.company_name {
				margin-top: 50rpx;

				font-size: 36rpx;
				font-weight: 500;
				color: #000000;
			}

			.company_describe {

				color: #666;
			}
		}
	}

	.company_introduce {

		margin: 30rpx;
		overflow: hidden;
		color: #666;
		font-size: 32rpx;
	}

	.work_place {

		margin: 100rpx 0;

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

	.recruit_info {

		.title {
			font-size: 36rpx;
			font-weight: 500;
			margin-left: 20rpx;
			margin-top: 40rpx;
		}

		.recruit_num {
			display: flex;
			padding: 20rpx;
			color: #666;
			border-bottom: 1rpx solid #eeeeee;

			text {
				margin-left: 20rpx;
				margin-top: 10rpx;
			}

		}

		.postion_item {
			border-bottom: 1rpx solid #eeeeee;

			.postion_name {
				margin: 20rpx;
				font-size: 32rpx;
			}

			.salary {
				position: absolute;
				right: 20rpx;
				margin-top: -60rpx;
				color: #D1372C;
			}

			.public_time {
				position: absolute;
				right: 20rpx;
				margin-top: -60rpx;
				color: #666;
			}

			.demand {
				margin: 20rpx;
				color: #666;
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

		.share {
			flex: 2;
			display: flex;
			flex-direction: column;
			justify-content: center;
			align-items: center;
			font-size: 24rpx;
			position: relative;

			button {
				position: absolute;
				top: 0;
				left: 0;
				width: 100%;
				height: 100%;
				opacity: 0;
			}
		}

		.collect {
			display: flex;
			flex: 3;
			height: 100rpx;
	
			background-color: #52a4f6;
			color: #fff;
			font-size: 30rpx;
			font-weight: 600;
			border-radius: 50rpx;
			justify-content: center;
			align-items: center;

		}
	}
</style>
