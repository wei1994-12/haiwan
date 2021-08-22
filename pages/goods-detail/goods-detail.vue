<template>
	<view style="padding: 10rpx;" v-if="data">
		<view class="lname">{{ details.lname }}</view>

		<swiper
			:indicator-dots="true"
			:autoplay="true"
			:interval="2500"
			:duration="250"
			circular
			style="width: 730rpx; height: 730rpx"
		>
			<swiper-item v-for="(item, index) in picList" :key="index">
				<image
					style="width: 100%; height: 100%;"
					:src="baseURL + item.md"
					mode=""
				></image>
			</swiper-item>
		</swiper>

		<view style="font-weight: bold;">{{ details.title }}</view>
		<view style="color: gray; margin: 15rpx 0; font-size: 0.9em;">
			{{ details.subtitle }}
		</view>
		<view style="color: red; font-weight: bold;">¥{{ details.price }}</view>

		<uni-collapse>
			<uni-collapse-item title="更多商品推荐" open>
				<uni-list>
					<!-- / 代表根目录, 即项目目录 -->
					<uni-list-item
						:to="`/pages/goods-detail/goods-detail?lid=${item.lid}`"
						v-for="(item, index) in laptopList"
						:key="index"
						:title="item.spec"
						clickable
						showArrow
					></uni-list-item>
				</uni-list>
			</uni-collapse-item>
		</uni-collapse>
		
		<view v-html="html_details"></view>
		
		<!-- 添加一个高度与 商品导航栏 高度相同的 空白view, 防止内容被遮住 -->
		<view style="height: 44px;"></view>
		<uni-goods-nav style="position: fixed; bottom: 0; width: 100%;" />
	</view>
</template>

<script>
export default {
	data() {
		return {
			lid: '',
			data: null,
			baseURL: 'http://101.96.128.94:9999/'
		};
	},
	onLoad(options) {
		const lid = options.lid;
		this.lid = lid; //局部变量lid 保存到 data的lid 中, 才能全局使用
	},
	mounted() {
		this.getData();
	},
	methods: {
		getData() {
			const url = 'http://101.96.128.94:9999/data/product/details.php';

			uni.request({
				url,
				method: 'GET',
				data: { lid: this.lid },
				success: res => {
					console.log(res);
					this.data = res.data;
				},
				fail: err => {
					console.log(err);
				},
				complete: () => {}
			});
		}
	},
	computed: {
		details() {
			return this.data.details;
		},
		family() {
			return this.data.family;
		},
		picList() {
			return this.details.picList;
		},
		laptopList() {
			return this.family.laptopList;
		},
		//详情的html, 由于服务器的原因 给的html不标准, 所以只能本地做修改
		html_details(){
			let details = this.details.details;
			
			// 把 <img 改为 <img width="100%"
			// g: 修饰符 代表全局匹配,  找到所有符合条件的字符串
			details = details.replace(/<img/g, '<img width="100%"')
			
			// src="img => src="http://101.96.128.94:9999/img
			details = details.replace(/src="img/g, 'src="http://101.96.128.94:9999/img')
			
			// 如果是 手机运行, 要求所有地址必须有前缀协议
			// src="//img20  改为  src="https://img20
			details = details.replace(/src="\/\/img20/g, 'src="https://img20')
			
			return details;
		}
	}
};
</script>

<style lang="scss">
.lname {
	border-bottom: 1px solid #ddd;
	padding: 10rpx;
}
</style>

<!-- "<div class="content_tpl"> <div class="formwork">   <div class="formwork_img"><br></div><div class="formwork_img">    <img alt="" class="" src="img/product/detail/57b15612N81dc489d.jpg">   </div>  </div>  <div class="formwork">   <div class="formwork_img">    <img alt="" class="" src="//img20.360buyimg.com/vc/jfs/t2683/60/4222930118/169462/233c7678/57b15616N1e285f09.jpg">   </div>  </div>  <div class="formwork">   <div class="formwork_text">    技术规格请前往 www.apple.com/cn/macbook-air/specs.html 查看完整内容。</div></div></div>" -->