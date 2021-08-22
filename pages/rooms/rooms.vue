<template>
	<view v-if="data" style="padding: 6rpx;">
		<uni-grid :column="2" :showBorder="false" :highlight="false" :square="false">
			<uni-grid-item v-for="(item, index) in data.list" :key="index">
				<view class="item">
					<view>
						<image :src="item.roomSrc" mode=""></image>
						<view>{{item.hn}}</view>
						<view>{{item.nickname}}</view>
					</view>
					<view>{{item.roomName}}</view>
				</view>
			</uni-grid-item>
		</uni-grid>
		
		<uni-load-more :status="status"></uni-load-more>
	</view>
</template>

<script>
export default {
	data() {
		return {
			status:'more',
			data: null,
			short_name: '',
			// #ifndef H5
			url: 'https://m.douyu.com/api/room/list',
			// #endif
			// #ifdef H5
			url: '/douyu/api/room/list'
			// #endif
		};
	},
	onLoad(options) {
		const { tag_name, short_name } = options;
		this.short_name = short_name;
		uni.setNavigationBarTitle({
			title: tag_name
		});
	},
	mounted() {
		this.getData();
	},
	onReachBottom() {
		this.getMore()
	},
	onPullDownRefresh() {
		uni.request({
			url: this.url,
			method: 'GET',
			data: {page: 1, type: this.short_name},
			success: res => {
				console.log(res);
				this.data = res.data.data;
				uni.stopPullDownRefresh();
				
				this.getMore()
			},
			fail: (err) => {
				console.log(err);
			},
			complete: () => {}
		});
	},
	methods: {
		getMore(){
			const {list, nowPage, pageCount} = this.data;
			
			if (nowPage == pageCount){
				this.status = 'noMore';
				return;
			}
			
			if (this.status == 'loading') return;
			
			this.status = 'loading'
			
			
			uni.request({
				url: this.url,
				method: 'GET',
				data: {page: nowPage+1, type: this.short_name},
				success: res => {
					console.log(res);
					//合并
					res.data.data.list = [...list, ...res.data.data.list]
					this.status = 'more'
					this.data = res.data.data;
				},
				fail: (err) => {
					console.log(err);
				},
				complete: () => {}
			});
		},
		getData() {
			uni.request({
				url: this.url,
				method: 'GET',
				data: { type: this.short_name, page: 1 },
				success: res => {
					console.log(res);
					this.data = res.data.data;
					
					this.getMore()
				},
				fail: err => {
					console.log(err);
				},
				complete: () => {}
			});
		}
	}
};
</script>

<style lang="scss">
.item{
	padding: 6rpx;
	margin-bottom: 10rpx;
	
	>view:first-child{
		position: relative;
		
		>image{
			width: 100%;
			height: 200rpx;
			border-radius: 8rpx;
			display: block;
		}
		
		// first-of-type:  view类型中的第一个
		>view:first-of-type{
			position: absolute;
			right: 0;
			top: 0;
			background-color: rgba(0,0,0,0.5);
			padding: 8rpx;
			color: white;
			border-radius: 6rpx;
			font-size: 0.6em;
		}
		
		>view:last-of-type{
			position: absolute;
			left: 0;
			bottom: 0;
			background-color: rgba(0,0,0,0.5);
			padding: 8rpx;
			color: white;
			border-radius: 6rpx;
			font-size: 0.6em;
		}
	}
	
	>view:last-child{
		padding: 10rpx;
	}
	
}
</style>
