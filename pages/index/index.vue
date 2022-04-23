<template>
	<view class="page">
		<view class="img_wrap">
			<image src="../../static/recruit.jpg" mode="widthFix"></image>
		</view>
		<view class="recruit_wrap">
			<view class="recruit_item">
				<view class="num">{{ companyNum }}</view>
				<view class="name">招聘企业</view>
			</view>
			<view class="recruit_item">
				<view class="num">{{ postionNum }}</view>
				<view class="name">发布职位</view>
			</view>
			<view class="recruit_item">
				<view class="num">{{ recruitNum }}</view>
				<view class="name">用人需求</view>
			</view>
			<view class="recruit_item">
				<view class="num">{{ tradeNum }}</view>
				<view class="name">职位种类</view>
			</view>
		</view>
		<!-- 热门职位 -->
		<view class="hot_postion_wrap">
			<view class="hot_postion_info">
				<view class="label"></view>
				<view class="title">热门职位</view>
			</view>
			<swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :indicator-active-color = isActive  style="height: 200rpx;">
				<swiper-item>
					<view class="hot_postion_item_wrap">
						<view class="hot_postion_list">
							<navigator v-for="(item,index) in tradeList" :key="index" v-if="index < 5" class="hot_postion_item"
							:url="'../postionList/postionList?tradeId=' + item.id" hover-class="none">
								<image :src="item.logo" mode="widthFix"></image>
								<view class="postion_name">{{ item.tradeName }}</view>
							</navigator>
						</view>
					</view>
				</swiper-item>
				<swiper-item v-if="tradeList.length > 5 ">
					<view class="hot_postion_item_wrap">
					<view class="hot_postion_list">
						<navigator v-for="(item,index) in tradeList" :key="index" v-if="index > 4 && index < 10" class="hot_postion_item"
						:url="'../postionList/postionList?tradeId=' + item.id" hover-class="none">
							<image :src="item.logo" mode="widthFix"></image>
							<view class="postion_name">{{ item.tradeName }}</view>
						</navigator>
					</view>
					</view>
				</swiper-item>


			</swiper>
		</view>

		<view class="company_wrap">
			<view class="company_info">
				<view class="label"></view>
				<view class="title">招聘企业</view>
				<navigator class="see_all" url="../companyList/companyList" hover-class="none">
					查看更多>
				</navigator>
			</view>
			<swiper :indicator-dots="true" :autoplay="true" :interval="3000" :duration="1000" :indicator-active-color = isActive 
			 indicator-color="#acacac" style="height: 320rpx;">
				<swiper-item>
					<navigator v-for="(item,index) in companyList" :key="index" v-if="index < 8" class="company_item" 
					:url="'../companyDetail/companyDetail?companyId=' + item.companyId "
					 hover-class="none">
						<image :src="item.logo" mode="widthFix"></image>
					</navigator>

				</swiper-item>
				<swiper-item v-if="companyList.length > 8">
					<navigator v-for="(item,index) in companyList" :key="index"  v-if="index > 8 && index < 16" class="company_item" 
					:url="'../companyDetail/companyDetail?companyId=' + item.companyId " hover-class="none">
						<image :src="item.logo" mode="widthFix"></image>
					</navigator>
				</swiper-item>
			</swiper>
		</view>
		<view class="postion_wrap">
			<view class="postion_info">
				<view class="label"></view>
				<view class="title">招聘职位</view>
			</view>
			<view class="postion_item_info">
				<navigator v-for="(item,index) in tradeList" :key="index" class="postion_item" hover-class="none" :url="'../postionList/postionList?tradeId=' + item.id">
					{{ item.tradeName }}
				</navigator>
			</view>

		</view>
	</view>
</template>

<script>
	export default {

		data() {
			return {
				companyList: [],
				tradeList:[],
				companyNum:0,
				postionNum:0,
				tradeNum:0,
				recruitNum:0,
				hotPostionItem1:[],
				isActive: '#FFFFFF'
				
			}
		},
		onShow() {
			var that = this;
			this.loadCompanyListData();
			this.loadTradeListDate();
			this.loadPostionListData();
			
			
		},
		methods: {
			loadTradeListDate:function(){
				this.$get('trade')
					.then(res => {
						if (res.code == 200) {
							if (res.result == 'success') {
								this.tradeList = res.data;
								this.tradeNum = this.tradeList.length;
								if(this.tradeList.length > 5) this.isActive = '#007AFF'
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
			loadPostionListData:function(){
				this.$get('postion')
					.then(res => {
						if (res.code == 200) {
							if (res.result == 'success') {
								
								res.data.forEach(v => {
									this.recruitNum += v.postion.num;
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
			loadCompanyListData: function() {
				this.$get('company')
					.then(res => {
						if (res.code == 200) {
							if (res.result == 'success') {
								console.log(res)
								this.companyList = [];
								res.data.forEach(v => {
									this.companyList.push(v.company);
								})
								if(this.companyList.length > 8) this.isActive = '#007AFF'
								this.companyNum = this.companyList.length;
								var sum = 0;
								this.companyList.forEach(item => {
									sum += item.postionNum;
								})
								this.postionNum = sum;
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
	.recruit_wrap {
		display: flex;
		position: absolute;
		margin-top: -70rpx;
		width: 90%;
		left: 50%;
		transform: translate(-50%);
		padding: 30rpx 0;
		color: #666;
		background-color: #FFFFFF;
		border-radius: 20rpx;
		-moz-box-shadow: 0 0 10px #f0f0f0;
		-webkit-box-shadow: 0 0 10px #f0f0f0;
		box-shadow: 0 0 10px #f0f0f0;

		.recruit_item {
			flex: 1;
			text-align: center;
		}
	}
	
	.hot_postion_info {
		margin-top: 100rpx;
		margin-left: 40rpx;
		display: flex;

		.label {
			width: 10rpx;
			background-color: #007AFF;
		}

		.title {
			margin-left: 10rpx;
			font-size: 36rpx;
		}

	}

	.company_info {
		margin-left: 40rpx;
		display: flex;
		
		.label {
			width: 10rpx;
			background-color: #007AFF;
		}

		.title {
			margin-left: 10rpx;
			font-size: 36rpx;
		}

		.see_all {
			color: #666;
			position: absolute;
			right: 20rpx;
			margin-top: 10rpx;
		}

	}

	.postion_info {
		margin-left: 40rpx;
		display: flex;

		.label {
			width: 10rpx;
			background-color: #007AFF;
		}

		.title {
			margin-left: 10rpx;
			font-size: 36rpx;
		}

	}

	.postion_item_info {
		display: flex;
		flex-wrap: wrap;
		margin-bottom: 100rpx;
		.postion_item {
			margin-top: 40rpx;
			width: 25%;
			text-align: center;
		}
	}

	swiper {
		width: 100%;
		display: flex;

		swiper-item {
			width: 100%;
			display: flex;
			flex-wrap: wrap;
			margin-left: 20rpx;
			margin-top: 20rpx;
			
			align-content: flex-start;
			.hot_postion_item_wrap {
				width: 100%;
				height: 100rpx;
				.hot_postion_list{
					display: flex;
					.hot_postion_item {
						width: 19%;
						display: flex;
						flex-direction: column;
						justify-content: center;
						align-items: center;
						
						image {
							width: 50%;
						}
					}
				}
				
				
			}

			.company_item {
				width: 20%;
				margin: 0 20rpx;
				
				display: flex;
				image {
					width: 100%;

				}
			}
		}

	}
</style>
