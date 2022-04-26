<template>
	<view class="postionCard_page">
		<view class="postion_info" hover-class="none" @click="handleClick">
			<view class="postion_name">{{ postionName }}</view>
			<view class="salary">{{ salary }}/月</view>
			<view class="demand">{{ demandEducation}}|{{ demandMajor }}|招收{{ num }}人</view>
			<view class="public_time">{{ publicDate}}</view>
			<view class="company_info">
				<image :src="logo" mode="widthFix"></image>
				<view class="company">
					<view class="company_name">{{ companyName}}</view>
					<view class="company_describe">{{ tradeName}} / {{ scale}}人</view>
				</view>
			</view>
		</view>

	</view>
</template>

<script>
	export default {
		props: {
			postionName:{
				type:String,
				default:''
			},
			salary:{
				type:String,
				default:''
			},
			demandEducation:{
				type:String,
				default:''
			},
			demandMajor:{
				type:String,
				default:''
			},
			publicDate:{
				type:String,
				default:''
			},
			logo:{
				type:String,
				default:''
			},
			companyName:{
				type:String,
				default:''
			},
			tradeName:{
				type:String,
				default:''
			},
			scale:{
				type:String,
				default:''
			},
			num:{
				type:String,
				default:''
			}
			
		},
		data() {
			return {

			};
		},
		methods: {
			handleClick: function() {
				this.$emit('postionDetail');
			}
		}
	}
</script>

<style lang="less">
	.postion_info {
		border-bottom: 1rpx solid #e5e5e5;
		width: 750rpx;

		.postion_name {
			margin: 10rpx 20rpx;
			color: #000000;
			font-size: 36rpx;
			font-weight: 500;

		}

		.salary {
			position: absolute;
			right: 20rpx;
			margin-top: -50rpx;
			color: #bf0000;
		}

		.public_time {
			color: #b4b4b4;
			position: absolute;
			right: 20rpx;
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
	}
</style>
