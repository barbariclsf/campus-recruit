<template>
	<view class="companyCard_page">
		<view class="company_info_wrap" @click="handleClick">
			<image :src="logo" mode="widthFix"></image>
			<view class="company">
				<view class="company_name">{{ companyName }}</view>
				<view class="company_describe">
					<text>{{ tradeName }} {{ scale}}人</text>
				</view>
				<view class="recruit_num">在招职位数:{{ postionNum }}</view>
			</view>
		</view>
	</view>

</template>

<script>
	export default {
		props: {
			logo: {
				type: String,
				default: ''
			},
			companyName: {
				type: String,
				default: ''
			},
			tradeName: {
				type: String,
				default: ''
			},
			scale: {
				type: String,
				default: ''
			},
			postionNum: {
				type: String,
				default: ''
			}
		},
		
		methods:{
			handleClick: function() {
				this.$emit('companyDetail');
			}
		}
	}
</script>

<style lang="less">
	.company_info_wrap {
		border-bottom: 1rpx solid #e5e5e5;
		width: 750rpx;
		display: flex;
		image {
			width: 20%;
			margin: 20rpx;
		}

		.company {
			display: flex;
			flex-direction: column;

			.company_name {
				color: #000000;
				margin: 20rpx;
				font-size: 32rpx;
				overflow: hidden;
			}

			.company_describe {
				margin-left: 20rpx;
				overflow: hidden;
				color: #666;

			}

			.recruit_num {
				margin: 20rpx;
				color: #007AFF;
				width: 500rpx;
			}
		}
	}
</style>
