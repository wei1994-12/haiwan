<template>
	<view style="display: flex;">
		<scroll-view
			scroll-y
			style="width: 30%;"
			:style="{ height: windowHeight + 'px' }"
		>
			<view>
				<view
					:class="{ cur: current == index }"
					class="item"
					v-for="(item, index) in hero"
					:key="index"
					@click="doCheck(index)"
					hover-class="active"
					hover-stay-time="200"
				>
					{{ item.title }}
				</view>
			</view>
		</scroll-view>

		<scroll-view
			scroll-y
			style="width: 70%;"
			:style="{ height: windowHeight + 'px' }"
			v-if="data && showRight"
		>
			<view class="area-right">
				<view style="display: flex;">
					<image
						:src="heroIcon"
						mode=""
						style="width: 180rpx; height: 180rpx;"
					></image>

					<view style="margin-left: 15rpx;">
						<view style="font-size: 1.2em; font-weight: bold;">
							{{ heroInfo.title }}
						</view>
						<view>昵称: {{ heroInfo.name }}</view>
						<view>金币: {{ heroInfo.goldPrice }}</view>
						<view>点券: {{ heroInfo.couponPrice }}</view>
					</view>
				</view>

				<uni-card title="背景故事" isFull>
					<view style="font-size: 0.8em">{{ heroInfo.shortBio }}</view>
				</uni-card>

				<uni-collapse>
					<uni-collapse-item title="使用建议">
						<uni-list>
							<uni-list-item
								v-for="(item, index) in heroInfo.allytips"
								:key="index"
								:title="item"
							></uni-list-item>
						</uni-list>
					</uni-collapse-item>

					<uni-collapse-item title="对战技巧">
						<uni-list>
							<uni-list-item
								v-for="(item, index) in heroInfo.enemytips"
								:key="index"
								:title="item"
							></uni-list-item>
						</uni-list>
					</uni-collapse-item>

					<uni-collapse-item title="技能说明">
						<uni-list>
							<uni-list-item
								v-for="(item, index) in spells"
								:key="index"
								:title="item.name"
								:thumb="item.abilityIconPath"
								thumbSize="lg"
								:note="item.description"
								direction="column"
							></uni-list-item>
						</uni-list>
					</uni-collapse-item>
					
					<uni-collapse-item title="皮肤">
						<uni-list>
							<uni-list-item
								v-for="(item, index) in skins"
								:key="index"
								direction="column"
								:note="item.description"
								:title="item.name"
								:thumb="item.iconImg"
								thumbSize="lg"
							></uni-list-item>
						</uni-list>
					</uni-collapse-item>
				</uni-collapse>
			</view>
		</scroll-view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			hero: [],
			current: 0,
			data: null,
			showRight:true,
		};
	},
	mounted() {
		this.getMenu();
	},
	methods: {
		getHeroDetail(heroId) {
			const url = `https://game.gtimg.cn/images/lol/act/img/js/hero/${heroId}.js`;

			uni.request({
				url,
				method: 'GET',
				data: {},
				success: res => {
					console.log(res);
					this.data = res.data;
					//刷新右侧  先false 然后true
					this.showRight = false;
					this.$nextTick(()=>{
						this.showRight = true;
					})
				},
				fail: err => {
					console.log(err);
				},
				complete: () => {}
			});
		},
		doCheck(index) {
			this.current = index;
			const hid = this.hero[index].heroId;
			this.getHeroDetail(hid);
		},
		getMenu() {
			const url =
				'https://game.gtimg.cn/images/lol/act/img/js/heroList/hero_list.js';

			uni.request({
				url,
				method: 'GET',
				data: {},
				success: res => {
					console.log(res);
					this.hero = res.data.hero;

					const hid = this.hero[0].heroId;
					this.getHeroDetail(hid);
				},
				fail: err => {
					console.log(err);
				},
				complete: () => {}
			});
		}
	},
	computed: {
		windowHeight() {
			return uni.getSystemInfoSync().windowHeight;
		},
		skins() {
			return this.data.skins;
		},
		spells() {
			return this.data.spells;
		},
		heroInfo() {
			return this.data.hero;
		},
		heroIcon() {
			return `https://game.gtimg.cn/images/lol/act/img/champion/${
				this.heroInfo.alias
			}.png`;
		}
	}
};
</script>

<style lang="scss">
.area-right {
	background-color: #eee;
	padding: 10rpx;
}

.item {
	padding: 15rpx;
	text-align: center;
	border-bottom: 1px solid #aaa;
	overflow: hidden;
	text-overflow: ellipsis;
	white-space: nowrap;

	&.cur {
		background-color: #eee;
		color: #007aff;
		font-weight: bold;
	}
}
</style>
