<template>
	<view>
		<view class="search_input">
			<navigator class="" target="" url="/pages/search/search" hover-class="navigator-hover" open-type="navigate">
				搜索
			</navigator>
		</view>
		
	</view>
</template>

<script>
	export default {
		data() {
			return {
				
			};
		}
	}
</script>

<style lang="less">
.search_input {

		height: 100rpx;
		padding: 10rpx 20rpx;

	}

	.search_input navigator {
		height: 100%;
		display: flex;
		justify-content: center;
		align-items: center;
		background-color: #fff;
		border-radius: 50rpx;
		background-color: #f5f5f5;
		color: #d3d3d3;
	}

</style>
