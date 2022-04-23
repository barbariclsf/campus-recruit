<template>
	<view class="user_page">
		<view class="user_wrap">
			<navigator url="userDetail/userDetail" hover-class="none" v-show="isLogin">
				<view class="user_info">
					<view class="user_name">{{ userInfo.userName }}</view>
					<view class="school_info" v-show="userInfo.schoolName != null">
						{{ userInfo.schoolName }}/{{userInfo.highestDegree}}/{{userInfo.major}}
					</view>
					<view class="school_info" v-show="userInfo.schoolName == null">
						快去完善简历吧
					</view>  
					<image :src="userInfo.avatar" mode="widthFix"></image>
				</view>
			</navigator>
			<navigator url="../login/login" hover-class="none" v-show="!isLogin" >
				<view class="user_info" >
					<view class="user_name">未登入/注册</view>
					<view class="school_info">点击头像即可进行登入</view>
					<image src="../../static/user.png" mode="widthFix"></image>
				</view>
			</navigator>
		</view>
		<view class="user_content">

			<navigator url="../onlineResume/onlineResume?type=0" hover-class="none">
				<view class="iconfont icon-zaixianjianli"> </view>
				<view class="item">在线简历</view>
				<view class="iconfont icon-xuanzeqixiayige"></view>
			</navigator>


			<navigator url="../attachmentResume/attachmentResume" hover-class="none">
				<view class="iconfont icon-fujianjianli"> </view>
				<view class="item">附件简历</view>
				<view class="load_resum">上传/管理简历附件</view>
				<view class="iconfont icon-xuanzeqixiayige"></view>
			</navigator>
			
			<navigator @click="handle" hover-class="none">
				<view class="iconfont icon-shimingrenzheng"> </view>
				<view class="item">我要认证</view>
				<view class="iconfont icon-xuanzeqixiayige"></view>
			</navigator>
			
			<navigator url="../collect/collect" hover-class="none">
				<view class="iconfont icon-wodeshoucang"> </view>
				<view class="item">我的收藏</view>
				<view class="iconfont icon-xuanzeqixiayige"></view>
			</navigator>
			
			<navigator url="../deliver/deliver" hover-class="none">
				<view class="iconfont icon-toudifankui"> </view>
				<view class="item">我的投递</view>
				<view class="iconfont icon-xuanzeqixiayige"></view>
			</navigator>
			
			<navigator url="" hover-class="none">
				<view class="iconfont icon-qiuzhizhuangtai"> </view>
				<view class="item">求职状态</view>
				<picker  @change="bindStatePickerChange" :range="stateArrays">
					<label>{{stateArrays[stateIndex]}}</label>
				</picker>
				<view class="iconfont icon-xuanzeqixiayige"></view>
			</navigator>
			
			<navigator url="" hover-class="none" @click="loginOut">
				<view class="iconfont icon-tuichudenglu"> </view>
				<view class="item">退出登录</view>
				<view class="iconfont icon-xuanzeqixiayige"></view>
			</navigator>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				stateArrays: ['积极求职', '已有Offer,停止求职', '没有Offer,暂不求职'],
				stateIndex:0,
				isLogin:false,
				userInfo:{},
				resume:{}
			}
		},
		onShow(){
			if (uni.getStorageSync('isLogin') != '') {
				this.isLogin = uni.getStorageSync('isLogin');
				this.userInfo = uni.getStorageSync('userInfo');
			}
		},
		methods: {
			loginOut: function() {
				if(this.isLogin == false){
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
					this.isLogin = false;
					uni.clearStorageSync();
					this.$toast("退出登录成功", 1000, 'none', true);
				}
				
			},
			handle:function(){
				if(this.userInfo.type == 0){
					uni.navigateTo({
						url:'../verify/verify'
					})
				}else{
					uni.navigateTo({
						url:'../recruiter/recruiter'
					})
					
				}
			},
			bindStatePickerChange(e){
				this.stateIndex = e.detail.value;
			},
		
		}
	}
</script>

<style lang="less">
	.user_wrap {
		width: 100%;
	
		background-color: #55aaff;
		display: flex;

		.user_info {
			display: flex;
			flex-direction: column;
			margin: 80rpx;
			color: #FFFFFF;

			.user_name {
				font-size: 36rpx;
				font-weight: 600;
			}
		}

		image {
			width: 150rpx;
			border-radius: 50%;
			position: absolute;
			right: 30rpx;
			top: 50rpx;


		}

	}
	.iconfont{
		color:#666;
	}
	.user_content{
		navigator{
			width: 100%;
			font-size: 32rpx;
			display: flex;
			.iconfont{
				margin: 40rpx 20rpx;
			}
			.item{
				margin-top: 40rpx;
			}
			.icon-xuanzeqixiayige{
				position: absolute;
				right: 0;
			}
			.load_resum{
				position: absolute;
				margin-top: 40rpx;
				right: 50rpx;
				color: #666;
			}
			picker{
				position: absolute;
				margin-top: 40rpx;
				right: 50rpx;
				color: #666;
			}
			
			
		}
	}
</style>
