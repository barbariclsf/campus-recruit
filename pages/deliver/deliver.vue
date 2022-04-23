<template>
	<view class="index_page">
	
		<view class="postion_list">
			<postionCard v-for="(item,index) in postionList" :key="index"
			:postionName = "item.postion.postionName"
			:salary = "item.postion.salary"
			:publicDate = "item.postion.publicDate"
			:demandEducation = "item.postion.demandEducation"
			:demandMajor = "item.postion.demandMajor"
			:logo = "item.company.logo"
			:companyName = "item.company.companyName"
			:tradeName = "item.trade.tradeName"
			:scale = "item.company.scale"
			@postionDetail="toPostionDetail(item.postion.postionId)">
			</postionCard>
		</view>
	</view>
</template>

<script>
	
	import postionCard from '@/components/postionCard.vue'
	
	export default {
		components: {
		
			postionCard,
			
		},
		data() {
			return {
				postionList: [],
			}
		},
		onShow() {
			this.loadPostionListData();
		},
		methods: {
			toPostionDetail:function(postionId){
				uni.navigateTo({
					url: '../postionDetail/postionDetail?postionId=' + postionId
				})
				
			},
			loadPostionListData: function() {
				this.$post('deliver/selectByDeliverId',{
					deliverId:uni.getStorageSync('userInfo').userId
				})
					.then(res => {
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

</style>
