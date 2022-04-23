<template>
	<view class="index_page">
		<search></search>

		<sl-filter :independence="true" :color="titleColor" :themeColor="themeColor" :menuList.sync="menuList" @result="result"></sl-filter>
		<view class="postion_list">
			<postionCard v-for="(item,index) in postionList" :key="index" :postionName="item.postion.postionName" :salary="item.postion.salary"
			 :publicDate="item.postion.publicDate" :demandEducation="item.postion.demandEducation" :demandMajor="item.postion.demandMajor"
			 :logo="item.company.logo" :companyName="item.company.companyName" :tradeName="item.trade.tradeName" :scale="item.company.scale"
			 @postionDetail="toPostionDetail(item.postion.postionId)">
			</postionCard>
		</view>
	</view>
</template>

<script>
	import slFilter from '@/components/sl-filter/sl-filter.vue';
	import search from '@/components/search.vue'
	import postionCard from '@/components/postionCard.vue';
	import companyCard from '@/components/companyCard.vue';
	export default {
		components: {
			slFilter,
			search,
			postionCard,
			companyCard

		},
		data() {
			return {
				postionList: [],
				themeColor: '#55aaff',
				titleColor: '#666666',
				filterResult: '',
				menuList: [{
						'title': '行业',
						'isMutiple': false,
						'key': 'jobType',
						'detailList': [{
								'title': '不限',
								'value': ''
							}]

					},
					{
						'title': '地区',
						'key': 'location',
						'isMutiple': false,
						'detailList': [{
								'title': '不限',
								'value': ''
							},
							{
								'title': '北京',
								'value': '北京'
							},
							{
								'title': '上海',
								'value': '上海'
							},
							{
								'title': '杭州',
								'value': '杭州'
							},
							{
								'title': '深圳',
								'value': '深圳'
							},
							{
								'title': '广州',
								'value': '广州'
							},

						]

					},
					{
						'title': '薪资',
						'key': 'salary',
						'isMutiple': false,
						'detailList': [{
								'title': '不限',
								'value': '不限'
							},
							
							{
								'title': '2000-5000',
								'value': '2000-5000'
							},
							{
								'title': '5000-8000',
								'value': '5000-8000'
							},
							{
								'title': '8000-1500',
								'value': '8000-1500'
							},
							{
								'title': '15000以上',
								'value': '15000以上'
							},
							

						]
					}
				]
			}
		},
		onLoad(option) {
			this.loadPostionListData(option);
			this.loadTradeListData();
			
			
					
			
		},
		methods: {
			//获取下拉选择框数据
			result(val) {
				
				var type = val.type;
				var value = val.value;
				var index = this.menuList.findIndex(v => v.title == type);
				if(index == 0){
					//行业
					let tradeList = uni.getStorageSync('tradeList');
					let postionList =  uni.getStorageSync('postionList');
					var tradeId = 0;
					this.postionList = uni.getStorageSync('postionList');
					tradeList.forEach(v => {
						if(v.tradeName === value){
							tradeId = v.id;
							
						}
					})
					
					let postionLists = [];
					postionList.forEach(v => {
						if( v.company.trade == tradeId){
							postionLists.push(v);
						}
					})
					this.postionList = postionLists;
					this.menuList[0].title = value;
				}else if(index == 1){
					let postionList =  uni.getStorageSync('postionList');
					let location = '';
					this.menuList[index].detailList.forEach(v => {
						if(v.value == value){
							location = v.value;
						}
					})
					let postionLists = [];
					postionList.forEach(v => {
						if( v.postion.location == location){
							postionLists.push(v);
						}
					})
					this.postionList = postionLists;
					
				}else if(index == 2){
					//薪水
					let postionList =  uni.getStorageSync('postionList');
					var salary = "";
					
					this.menuList[index].detailList.forEach(v => {
						if(v.value == value){
							salary = v.value;
						}
					})
					let postionLists = [];
					postionList.forEach(v => {
						if( v.postion.salary == salary){
							postionLists.push(v);
						}
					})
					this.postionList = postionLists;
					
				}else{
					//不限
					console.log("-111")
					this.postionList = uni.getStorageSync('postionList');;
				}
				
				
			},
			toPostionDetail: function(postionId) {
				uni.navigateTo({
					url: '../postionDetail/postionDetail?postionId=' + postionId
				})

			},
			loadTradeListData:function(){
				this.$get('trade')
					.then(res => {
						if (res.code == 200) {
							if (res.result == 'success') {
								console.log("2")
								uni.setStorageSync("tradeList",res.data);
								console.log(res.data);
								this.menuList[0].detailList = [{'title': '不限','value':'不限'}];
								res.data.forEach(v => {
									this.menuList[0].detailList.push({'title':v.tradeName,'value':v.tradeName});
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
			loadPostionListData: function(option) {

				this.$get('postion')
					.then(res => {
						if (res.code == 200) {
							if (res.result == 'success') {
								console.log(res.data)
								this.postionList = res.data;
								uni.setStorageSync('postionList', res.data);
								if(option != ''){
					
									let postionLists = [];
									this.postionList.forEach(v => {
										if( v.trade.id == option.tradeId){
											postionLists.push(v);
											
										}
									})
									this.postionList = postionLists;
								}

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
