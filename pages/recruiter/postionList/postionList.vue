<template>
	<view class="index_page">
		<view class="postion_list">
			<view class="postion_item" v-for="(item,index) in postionList" :key="index"  hover-class="none" @click="handleClick">
				<view class="postion_name">{{ item.postion.postionName }}</view>
				<view class="salary">{{ item.postion.salary }}/月</view>
				<view class="demand">{{ item.postion.demandEducation}}|{{ item.postion.demandMajor }}|招收{{ item.postion.num }}人</view>
				<view class="public_time">{{ item.postion.publicDate}}</view>
				<view class="company_info">
					<image :src="item.company.logo" mode="widthFix"></image>
					<view class="company">
						<view class="company_name">{{ item.company.companyName}}</view>
						<view class="company_describe">{{ item.trade.tradeName}} / {{ item.company.scale}}人</view>
					</view>
				</view>
				<view class="operation">
					<view class="iconfont icon-shuxie" @click="updatePostion(item)"></view>
					<view class="iconfont icon-changyonggoupiaorenshanchu" @click="deletePostion(item.postion.postionId)"></view>
				</view>
				
			</view>
		</view>
		
		<button class="public_btn" @click="publicPostion">去发布</button>
	</view>
</template>

<script>
	

	export default {
		
		data() {
			return {
				postionList: [],
				
			}
		
	},
	onShow() {
		this.loadPostionData();
	},
	methods: {
		deletePostion:function(pId){
			this.$post('/postion/deletePostion',{
				postionId:pId
			}).then(res => {
					if (res.code == 200) {
						if (res.result == 'success') {
							this.loadPostionData();
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
		updatePostion:function(data){
			
			uni.setStorageSync('postionDetail',data);
			uni.navigateTo({
				url:'../publicPostion/publicPostion?postionId=' + data.postion.postionId
			})
		},
		toPostionDetail:function(postionId){
			uni.navigateTo({
				url: '../postionDetail/postionDetail?postionId=' + postionId
			}).then(res => {
					if (res.code == 200) {
						if (res.result == 'success') {
							console.log(res);
							this.postionList = res.data;
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
		publicPostion:function(){
			uni.navigateTo({
				url:'../publicPostion/publicPostion?postionId=0'
			})
		},
		loadPostionData: function() {
			this.$post('postion/selectByPubId', {
					userId:uni.getStorageSync('userInfo').userId
				}).then(res => {
					if (res.code == 200) {
						if (res.result == 'success') {
							console.log(res);
							this.postionList = res.data;
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
	.postion_list{
		background-color:#f9f9f9 ;
		padding-bottom: 120rpx;
	}
	.postion_item {
		background-color: #FFFFFF;
		margin: 20rpx;
		border: 1rpx solid #e5e5e5;
		width: 95%;
		border-radius: 20rpx;
		
		.postion_name {
			margin: 10rpx 20rpx;
			color: #000000;
			font-size: 36rpx;
			font-weight: 500;
	
		}
	
		.salary {
			position: absolute;
			right: 40rpx;
			margin-top: -50rpx;
			color: #bf0000;
		}
	
		.public_time {
			color: #b4b4b4;
			position: absolute;
			right: 40rpx;
			margin-top: -40rpx;
		}
	
		.demand {
			margin-left: 20rpx;
			color: #666;
		}
	
		.company_info {
			display: flex;
	
			image {
				margin: 20rpx;
				width: 15%;
			}
	
			.company {
				display: flex;
				flex-direction: column;
				margin: 30rpx 20rpx;
	
				.company_name {
					color: #000000;
					font-size: 32rpx;
					margin-bottom: 10rpx;
				}
	
				.company_describe {
					color: #666;
				}
			}
			
		}
		.icon-shuxie{
			position: absolute;
			right: 120rpx;
			margin-top: -70rpx;
		}
		.icon-changyonggoupiaorenshanchu{
			position: absolute;
			right: 40rpx;
			margin-top: -70rpx;
		}
	}
	.public_btn{
		position: fixed;
		bottom: 0rpx;
		width: 95%;
		border-radius: 20rpx;
		height: 80rpx;
		margin-left: 20rpx;
		margin-bottom: 20rpx;
		background-color: #4e9dec;
		color: #FFFFFF;
		display: flex;
		justify-content: center;
		align-items: center;
	}
</style>
