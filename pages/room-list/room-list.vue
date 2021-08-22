<template>
  <view v-if="data">
    <uni-grid :column="2" :showBorder="false" :square="false" :highlight="false">
      <uni-grid-item v-for="(item, index) in data.list">
        <view style="width: 360rpx; position: relative;">
          <image
            style="width: 100%; height: 250rpx; display: block;"
            :src="item.roomSrc"
            mode=""
          ></image>
          <view style="position: absolute; right: 0; top: 0; font-size: 0.7em; color: white; background-color: rgba(0,0,0,0.5);">{{ item.hn }}</view>
          <view style="position: absolute; bottom: 0;left: 0; display: flex; align-items: center;background-color: rgba(0,0,0,0.5);">
            <image
              style="width: 40rpx; height: 40rpx;"
              :src="item.avatar"
              mode=""
            ></image>
            <view style="font-size: 0.7em; color: white;">{{ item.nickname }}</view>
          </view>
        </view>
        <view style="font-size: 0.8em; margin: 10rpx 10rpx 20rpx;">{{ item.roomName }}</view>
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
      // #ifndef H5
      url: 'https://m.douyu.com/api/room/list',
      // #endif
      // #ifdef H5
      url: '/douyu/api/room/list',
      // #endif
      type: '',
      data: null
    };
  },
  onLoad(e) {
    this.type = e.short_name;
    uni.setNavigationBarTitle({
      title: e.tag_name
    });
  },
  mounted() {
    this.getData();
  },
  onReachBottom() {
    const {nowPage,pageCount} = this.data;
    if (nowPage == pageCount){
      this.status = 'noMore';
      return;
    }
    
    this.getMore()
  },
  onPullDownRefresh() {
    uni.request({
      url: this.url,
      method: 'GET',
      data: {page:1, type:this.type},
      success: res => {
        this.data = res.data.data
        this.getMore()
        
        uni.stopPullDownRefresh()
      },
      fail: () => {},
      complete: () => {}
    });
  },
  methods: {
    getMore(){
      this.status = 'loading';
      
      uni.request({
        url: this.url,
        method: 'GET',
        data: {page: this.data.nowPage+1, type: this.type},
        success: res => {
          console.log(res);
          res.data.data.list = [...this.data.list, ...res.data.data.list]
          this.data = res.data.data;
        },
        fail: () => {},
        complete: () => {}
      });
    },
    getData() {
      uni.request({
        url: this.url,
        method: 'GET',
        data: { page: 1, type: this.type },
        success: res => {
          console.log(res);

          this.data = res.data.data;
          
          // 默认数据太少, 导致大屏手机不足一屏, 无法触发加载更多操作
          this.getMore()
        },
        fail: () => {},
        complete: () => {}
      });
    }
  }
};
</script>

<style></style>
