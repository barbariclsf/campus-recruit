<template>
	<view class="box">
		<!-- 组件内关闭 -->
		
		<!--  -->
		<!-- 遮罩层 -->
		<view class="mask" v-show="tag==true || Uptag == true" @click="close()"></view>
		<!-- 插槽 -->
		<slot></slot>
		<!-- 弹窗 -->
		
		<view class="window all-transition" :style="direction=='bottom' ? objDirection : ''" v-show="direction=='bottom'">
			<view class="title">确认简历投递</view>
			<view class="resume_wrap">
				<view class="reume_item" v-if="resumeName != ''">
					<image class="fileImg" src="../static/file.png" mode="widthFix"></image>
					<view class="resume_info">
						<view class="type">附件简历</view>
						<view class="resume_name">{{ resumeName }}</view>
						
					</view>
					<view class="browser" @click="openPdf(resumeUrl)">预览 > |</view>
					<view v-if="isChoose" class="choose" @click="handleChoose(1)"></view>
					<image v-if="!isChoose" class="chooseImg" src="../static/choose.png" mode="widthFix" @click="handleChoose(1)"></image>
				</view>
				
				<view class="reume_item">
					<image class="fileImg" src="../static/file.png" mode="widthFix"></image>
					<view class="resume_info">
						<view class="type">在线简历</view>
						<view class="resume_name">{{ userName }}在线简历</view>
						
					</view>
					<view class="browser" @click="toUser">预览 > |</view>
					<view v-if="!isChoose" class="choose" @click="handleChoose(0)"></view>
					<image v-if="isChoose"  class="chooseImg" src="../static/choose.png" mode="widthFix" @click="handleChoose(0)"></image>
				</view>
			</view>
			<button  class="bottom-btn" @tap="confirmDeliver">确认投递</button>
		</view>
		
	</view>
</template>

<script>
	// 1.如果先要在组件内部控制显示与隐藏设置bodyBtn为true即可
	// 2.
	export default {
		props: {
			direction: String, //方向
			Uptag: Boolean, //判断点击事件
			bodyBtn: Boolean ,//组件内容控制显示隐藏
			resumeName:String,
			resumeUrl:String,
			userName:String,
			
		},
		data() {
			return {
				tag: false, //组件内关闭的变量
				isChoose:false,//简历选择
				resumeType:1
				
			}
		},
		computed: {
			//计算函数
			objDirection() {
				if (this.direction == 'bottom') {
					if (this.tag || this.Uptag == true) {
						return "transform: translateY(0);"
					} else {
						return "transform: translateY(200%);"
					}
				} else if (this.direction == 'right') {
					if (this.tag || this.Uptag == true) {
						return "transform: translateX(0);"
					} else {
						return "transform: translateX(100%);"
					}
				} else if (this.direction == 'left') {
					if (this.tag || this.Uptag == true) {
						return "transform: translateX(0);"
					} else {
						return "transform: translateX(-100%);"
					}
				} else if (this.direction == 'top') {
					if (this.tag || this.Uptag == true) {
						return "transform: translateY(0);"
					} else {
						return "transform: translateY(-100%);"
					}
				}
			}
		},
		methods: {
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
			toUser:function(){
				this.$emit("toOnlineResume")
			},
			handleChoose:function(e){
				
				this.resumeType = e;
				this.isChoose = !this.isChoose;
			},
			popBtn() {
				this.tag = !this.tag
			},
			
			confirmDeliver() {
				if (this.tag == true) this.tag = false //组件内关闭事件
				this.$emit('close', false) //组件外关闭事件
				this.$emit('deliver',this.resumeType);
			}
		}
	}
</script>

<style lang="less">
	.mask {
		position: fixed;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		width: 100%;
		height: 100%;
		background-color: rgba(0, 0, 0, 0.2);
		z-index: 1;
	}

	.all-transition {
		position: absolute;
		transition: all 0.3s;
		-moz-transition: all 0.3s;
		/* Firefox 4 */
		-webkit-transition: all 0.3s;
		/* Safari and Chrome */
		-o-transition: all 0.3s;
		background-color: #FFFFFF;
		z-index: 999;

	}

	.window {
		display: flex;
		flex-direction: column;
		position: fixed;
		bottom: 0;
		width: 100%;
		border-radius: 40rpx 40rpx 0 0;
		background-color: #fafafa;
	}

	.window-right {
		/* 如果不想让右侧全屏占满,将top值删掉就可 */
		top: 0;
		right: 0;
		width: 200rpx;
		height: 100%;
		border-radius: 15rpx 0 0 15rpx;
	}

	.window-left {
		/* 如果不想让右侧全屏占满,将top值删掉就可 */
		top: 0;
		left: 0;
		width: 200rpx;
		height: 100%;
		border-radius: 0 15rpx 15rpx 0;
	}

	.window-top {
		left: 50%;
		margin-left: -300rpx;
		/* 如果不想让右侧全屏占满,将top值删掉就可 */
		top: 0;
		width: 600rpx;
		height: 500rpx;
		border-radius: 0 0 15rpx 15rpx;
	}

	.bottom-btn {
		position: fixed;
		display: flex;
		bottom: 10rpx;
		width: 95%;
		height: 90rpx;
		margin-left: 20rpx;
		color: #FFFFFF;
		background-color: #52a4f6;
		border-radius: 50rpx;
		justify-content: center;
		align-items: center;
	}

	.title{
		margin-top: 30rpx;
		margin-left: 30rpx;
		font-size: 32rpx;
		font-weight: 600;
	}
	.resume_wrap {
		display: flex;
		flex-direction: column;
		padding-bottom: 120rpx;
		.reume_item {
			display: flex;
			width: 95%;
			margin-left: 20rpx;
			margin-top: 20rpx;
			background-color: #FFFFFF;
			.fileImg {
				width: 20%;
				margin-left: 20rpx;
			}

			.browser {
				margin-top: 60rpx;
				margin-left: 20rpx;
				color: #666;
			}
			.choose{
				width: 50rpx;
				height: 50rpx;
				border-radius: 50%;
				border:1rpx solid #666;
				margin-top: 50rpx;
				margin-left: 20rpx;
			}

			.chooseImg {
				width: 8%;
				margin-top: 40rpx;
				margin-left: 20rpx;
			}


			.resume_info {
				display: flex;
				flex-direction: column;
				margin-top: 20rpx;
				margin-left: 20rpx;
				font-size: 36rpx;
				.type{
					font-size: 26rpx;
				}
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

		}

	}
</style>
