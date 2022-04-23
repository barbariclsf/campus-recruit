<template>
	<view class="companylist_page">
		<search></search>
		<sl-filter :independence="true" :color="titleColor" :themeColor="themeColor" :menuList.sync="menuList" @result="result"></sl-filter>
		<view class="company_list">
			<companyCard v-for="(item,index) in companyList" :key="index" @companyDetail="toCompanyDetail(item.company.companyId)"
			 :logo="item.company.logo" :companyName="item.company.companyName" :tradeName="item.trade.tradeName" :scale="item.company.scale"
			 :postionNum="item.company.postionNum">
			</companyCard>
		</view>
	</view>
</template>

<script>
	import slFilter from '@/components/sl-filter/sl-filter.vue';
	import search from '@/components/search.vue'
	import companyCard from '@/components/companyCard.vue';
	import postionCard from '@/components/companyCard.vue';

	export default {
		components: {
			slFilter,
			companyCard,
			search,
			postionCard
		},
		data() {
			return {
				companyList: [],
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
								'title': '北京市',
								'value': '北京市'
							},
							{
								'title': '上海市',
								'value': '上海市'
							},
							{
								'title': '杭州市',
								'value': '杭州市'
							},
							{
								'title': '深圳市',
								'value': '深圳市'
							},
							{
								'title': '广州市',
								'value': '广州市'
							},

						]

					},
					{
						'title': '规模',
						'key': 'scale',
						'isMutiple': false,
						'detailList': [{
								'title': '不限',
								'value': '不限'
							},

							{
								'title': '100-500',
								'value': '100-500'
							},
							{
								'title': '500-1000',
								'value': '500-1000'
							},
							{
								'title': '1000-10000',
								'value': '1000-10000'
							},
							{
								'title': '10000以上',
								'value': '10000以上'
							},


						]
					}
				]
			}
		},
		onShow() {
			this.loadPostionListData();
			this.loadTradeListData();
		},
		methods: {

			result(val) {
				var type = val.type;
				var value = val.value;
				var index = this.menuList.findIndex(v => v.title == type);
				if (index == 0) {
					//行业
					let tradeList = uni.getStorageSync('tradeList');
					let companyList = uni.getStorageSync('companyList');
					var tradeId = 0;
					this.companyList = uni.getStorageSync('companyList');
					tradeList.forEach(v => {
						if (v.tradeName === value) {
							tradeId = v.id;

						}
					})

					let companyLists = [];
					companyList.forEach(v => {
						if (v.company.trade == tradeId) {
							companyLists.push(v);
						}
					})
					this.companyList = companyLists;
					
				} else if (index == 1) {
					let companyList = uni.getStorageSync('companyList');
					var location = '';
					this.menuList[index].detailList.forEach(v => {
						if (v.value == value) {
							location = v.value;
						}
					})
					let companyLists = [];
					companyList.forEach(v => {
						if (v.company.location == location) {
							companyLists.push(v);
						}
					})
					this.companyList = companyLists;

				} else if (index == 2) {
					//规模
					let companyList = uni.getStorageSync('companyList');
					var scale = "";
					this.menuList[index].detailList.forEach(v => {
						if (v.value == value) {
							scale = v.value;
						}
					})
					let companyLists = [];
					companyList.forEach(v => {
						if (v.company.scale == scale) {
							companyLists.push(v);
						}
					})
					this.companyList = companyLists;

				} else {
					//不限
					
					this.companyList = uni.getStorageSync('companyList');;
				}
			},

			toCompanyDetail: function(companyId) {

				uni.navigateTo({
					url: '../companyDetail/companyDetail?companyId=' + companyId
				})

			},
			loadTradeListData: function() {
				this.$get('trade')
					.then(res => {
						if (res.code == 200) {
							if (res.result == 'success') {
								uni.setStorageSync("tradeList", res.data);
								console.log(res.data);
								this.menuList[0].detailList = [{
									'title': '不限',
									'value': '不限'
								}];
								res.data.forEach(v => {
									this.menuList[0].detailList.push({
										'title': v.tradeName,
										'value': v.tradeName
									});
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
			loadPostionListData: function() {
				this.$get('company')
					.then(res => {
						if (res.code == 200) {
							if (res.result == 'success') {
								console.log(res);
								this.companyList = res.data;
								uni.setStorageSync('companyList', res.data);
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
