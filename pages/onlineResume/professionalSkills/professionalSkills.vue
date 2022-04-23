<template>
	<view class="profession_skills_page">
		<view class="skill_wrap">
			<view class="skill_title">专业技能</view>
			<view class="skill_item">
				<view class="title">技能名称</view>
				<input type="text" v-model="skillName" />
			</view>
			<view>
				<view class="title">描述技能</view>
				<rich-text-editor v-model="description"></rich-text-editor>
			</view>
		</view>
		<button class="btn_remove" @click="deleteEduExp">删除</button>
		<button class="btn_save" @click="saveEduExp">保存</button>
	</view>
</template>

<script>
	import RichTextEditor from "@/components/RichTextEditor.vue"
	export default {
		components: {
			RichTextEditor
		},
		data() {
			return {
				id: 0,
				skillName: '请输入..',
				description: '描述技能'

			}
		},
		watch: {
			richText() {
				console.log(this.description);
			}
		},
		onLoad(option) {
			this.id = option.id;
			if (this.id != 0) {
				var proSkills = wx.getStorageSync('resume').proSkills;
				var index = proSkills.findIndex(v => v.id == this.id);
				this.skillName = proSkills[index].skillName;
				this.description = proSkills[index].description;
			}

		},
		methods: {
			deleteEduExp: function() {
				console.log(this.id)
				if (this.id == 0) {
					this.$toast('先保存才能删除哦', 1000, 'none', true);
				} else {
					this.$post('resume/deleteProSkill', {
						id: this.id
					}).then(res => {
						console.log(res)
						if (res.code == 200) {
							if (res.result == 'success') {
								//更新缓存
								wx.setStorageSync('resume', res.data);
								this.$toast('删除成功', 1000, 'none', true);
								setTimeout(() => {
									uni.navigateBack({
										delta: 1
									})
								}, 1000)
							} else {
								this.$toast('删除失败', 1000, 'none', true);
							}
						} else {
							this.$toast('出错了', 1000, 'none', true);
						}
					}).catch(err => {
						this.$toast(err, 1000, 'none', true);
					})
				}
			},
			saveEduExp: function() {
				if (this.id == 0) {
					this.$post('resume/saveProSkill', {
						userId: wx.getStorageSync('userInfo').userId,
						skillName: this.skillName,
						description: this.description
					}).then(res => {
						console.log(res)
						if (res.code == 200) {
							if (res.result == 'success') {
								//更新缓存
								wx.setStorageSync('resume', res.data);
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
				} else {
					this.$post('resume/updateProSkill', {
						id: this.id,
						skillName: this.skillName,
						description: this.description
					}).then(res => {
						console.log(res)
						if (res.code == 200) {
							if (res.result == 'success') {
								//更新缓存
								wx.setStorageSync('resume', res.data);
								this.$toast('更新成功', 1000, 'none', true);
								setTimeout(() => {
									uni.navigateBack({
										delta: 1
									})
								}, 1000)
							} else {
								this.$toast('更新失败', 1000, 'none', true);
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
	}
</script>

<style lang="less">
	.skill_title {
		margin: 20rpx;
		font-size: 40rpx;
		font-weight: 600;
	}

	.title {
		font-size: 32rpx;
		padding-top: 10rpx;
		padding-left: 20rpx;
	}

	.skill_item {
		background-color: #FFFFFF;
		border-bottom: 1rpx solid #F1F1F1;
		input {
			padding: 10rpx 20rpx;
			width: 100%;
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

	.btn_remove {
		background-color: #55aaff;
		position: fixed;
		width: 40%;
		margin-left: 30rpx;
		bottom: 10rpx;
		color: #FFFFFF;
		font-size: 36rpx;

	}

	.btn_save {
		background-color: #55aaff;
		position: fixed;
		width: 40%;
		margin-left: 420rpx;
		bottom: 10rpx;
		color: #FFFFFF;
		font-size: 36rpx;
	}
</style>
