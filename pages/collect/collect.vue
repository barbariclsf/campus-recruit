<template>
	<view class="collect_page">
		
		<tabs :tabs="tabs" @tabItemChange="handleTabItemChange">
			<block v-if="tabs[0].isActive">
				<view class="company_list">
					<companyCard v-for="(item,index) in companyList" :key="index"
					@companyDetail="toCompanyDetail(item.company.companyId)"
					:logo="item.company.logo"
					:companyName="item.company.companyName"
					:tradeName="item.trade.tradeName"
					:scale="item.company.scale"
					:postionNum="item.company.postionNum">
					</companyCard>
				</view>
			</block>
			<block v-else-if="tabs[1].isActive">
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
					
				
			</block>
		</tabs>
		

	</view>
</template>

<script>
	import tabs from '@/components/tabs.vue';
	import postionCard from '@/components/postionCard.vue';
	import companyCard from '@/components/companyCard.vue';
	
	export default {
		components: {
			tabs,
			postionCard,
			companyCard,
			
		},
		data() {
			return {
				postionList:[],
				companyList:[],
				tabs: [{
						id: 0,
						value: "企业",
						isActive: true
					},
					{
						id: 1,
						value: "职位",
						isActive: false
					}

				]

			}
		},
		onLoad() {
			this.loadCollectDate();
		},
		methods: {
			loadCollectDate:function(){
				if(!wx.getStorageSync('isLogin')){
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
					this.$post('collect/selectCollect',{
						userId:wx.getStorageSync('userInfo').userId
					}).then(res => {
						if (res.code == 200) {
							if (res.result == 'success') {
								this.companyList = res.arrayList;
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
				
			},
			toPostionDetail:function(postionId){
				uni.navigateTo({
					url: '../postionDetail/postionDetail?postionId=' + postionId
				})
				
			},
			toCompanyDetail:function(companyId){
				
				uni.navigateTo({
					url: '../companyDetail/companyDetail?companyId=' + companyId
				})
				
			},
			handleTabItemChange(index) {
				console.log(index)
				let oldTabs = this.tabs;
				oldTabs.forEach((v, i) => i == index ? v.isActive = true : v.isActive = false);
				this.tabs = oldTabs;
			}
		}
	}
</script>

<style lang="less">
	
	
	
</style>
