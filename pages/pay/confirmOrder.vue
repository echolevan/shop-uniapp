<template>
  <view class="container">
    <view class="spinner"></view>
    <text class="loading-text">正在跳转</text>
  </view>
</template>

<script>

import {confirmOrder} from "@/api/api";

export default {
  components: {},
  mixins: [],
  data() {
    return {
      goodsId: '',
      goodsAttr: '',
      originalPrice: '',
      discountPrice: '',
    };
  },
  onLoad(options) {
    this.goodsId = options.goodsId;
    this.originalPrice = options.originalPrice;
    this.discountPrice = options.discountPrice;
    this.confirmOrder();
  },
  onShow() {
  },
  methods: {
    confirmOrder() {
      let that = this;
      confirmOrder({
        goodsId: this.goodsId,
        goodsAttr: this.goodsAttr,
        originalPrice: this.originalPrice,
        discountPrice: this.discountPrice,
      }).then(({data}) => {
        const carId = data.carId;
        const token = data.token;

        uni.showToast({
          title: '获取订单成功，正在跳转',
          icon: 'none'
        })

        that.$store.commit('LOGIN', {
          token: token.token,
          time: token.expires_time
        });
        if (carId) {
          uni.navigateTo({
            url: '/pages/goods/order_confirm/index?new=1&cartId=' + carId,
          })
        }
      })
    },
  }
};
</script>

<style scoped>
.container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background-color: #ffffff;
}

/* 微信绿色 Loading 圆环 */
.spinner {
  width: 100rpx;
  height: 100rpx;
  border: 10rpx solid #e5e5e5; /* 灰色背景环 */
  border-top-color: #07c160; /* 微信绿色部分 */
  border-radius: 50%;
  animation: spin 1s linear infinite;
  margin-bottom: 30rpx;
}

@keyframes spin {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}

.loading-text {
  font-size: 30rpx;
  font-weight: 300;
  color: #666;
}
</style>