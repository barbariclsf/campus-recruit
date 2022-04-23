<template>
	<view class="jobIntention_page">
		<view class="intention_wrap">
			<view class="intention_title">管理求职意向</view>
			<view class="intention_item">
				<view class="title">期望行业</view>
				<input type="text" v-model="trade" />
			</view>
			<view class="intention_item">
				<view class="title">期望职位</view>
				<input type="text" v-model="postion" />
			</view>

			<view class="intention_item">
				<view class="title">期望薪资</view>
				<picker @change="bindSalaryPickerChange" :range="salaryArrays">
					<label>{{salaryArrays[salaryIndex]}}</label>
				</picker>
				<view class="iconfont icon-xuanzeqixiayige"></view>
			</view>
			<view class="intention_item">
				<view class="title">期望地点</view>
				<input type="text" v-model="location" />
			</view>
		</view>
		<button class="btn_save" @click="saveJobIntention">保存求职意向</button>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				salaryArrays: ['2000-5000', '5000-8000', '8000-1500', '15000以上'],
				salaryIndex: 0,
				trade: '请输入',
				postion: '请输入',
				salary: '',
				location: '请输入',

			}
		},
		onShow() {
			var jobIntention = wx.getStorageSync('resume').jobIntention;
			if (jobIntention != null) {
				this.trade = jobIntention.trade;
				this.postion = jobIntention.postion;
				var index = this.salaryArrays.findIndex(v => v == jobIntention.salary);
				if (index == -1) index = 0;
				this.salaryIndex = index;
				this.location = jobIntention.location;
			}
		},
		methods: {
			
			bindSalaryPickerChange(e) {
				this.salaryIndex = e.detail.value;

				this.salary = this.salaryArrays[this.salaryIndex];
			},
			saveJobIntention: function() {
				this.$post('resume/saveJobIntention', {
					userId: wx.getStorageSync("userInfo").userId,
					trade: this.trade,
					postion: this.postion,
					salary: this.salaryArrays[this.salaryIndex],
					location: this.location
				}).then(res => {
					console.log(res)
					if (res.code == 200) {
						if (res.result == 'success') {
							//更新缓存
							wx.setStorageSync('jobIntention', res.data);
							this.$toast('保存成功', 1000, 'none', true);
							setTimeout(() => {
								uni.navigateBack({
									delta: 1
								})
							}, 1000)
						} else {
							this.$toast('保存失败', 1000, 'none', true);
						}
					} else {
						this.$toast('出错了', 1000, 'none', true);
					}
				}).catch(err => {
					this.$toast(err, 1000, 'none', true);
				})
			}
		}
	}
</script>

<style lang="less">
	.intention_title {
		margin: 10rpx 20rpx;
		font-size: 40rpx;
		font-weight: 600;
	}

	.intention_item {
		background-color: #FFFFFF;
		border-bottom: 1rpx solid #F1F1F1;

		.title {
			font-size: 32rpx;
			padding-top: 10rpx;
			padding-left: 20rpx;
		}

		input {
			padding: 10rpx 20rpx;
			width: 100%;
		}

		picker {
			padding: 10rpx 20rpx;

		}

		.icon-xuanzeqixiayige {
			position: absolute;
			right: 20rpx;
			margin-top: -50rpx;
		}
	}

	.btn_save {
		background-color: #55aaff;
		position: fixed;
		width: 95%;
		margin-left: 20rpx;
		bottom: 10rpx;
		color: #FFFFFF;
		font-size: 36rpx;
	}
</style>
