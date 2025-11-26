<template>
  <view class="container">
    <view class="spinner"></view>
    <text class="loading-text">正在跳转...</text>
  </view>
</template>

<script>

import {confirmOrder} from "@/api/api";
const app = getApp();

export default {
  components: {},
  mixins: [],
  data() {
    return {
      goodsId: '',
      goodsAttr: '',
      originalPrice: '',
      discountPrice: '',
      qrCode: '',
    };
  },
  onLoad() {
    console.log('globalData', app.globalData);
    this.qrCode = app.globalData.aliQrCode || '未知';
    console.log('qrCode', this.qrCode);
    const params = this.parseQueryParams(this.qrCode);
    console.log('params', params);

    this.goodsId = params.goodsId;
    this.originalPrice = params.originalPrice;
    this.discountPrice = params.discountPrice;

    console.log(this.discountPrice);
    console.log(this.originalPrice);
    console.log(this.goodsId);
    this.confirmOrder();
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
    parseQueryParams(url) {
      if (!url || typeof url !== "string") return {};

      const index = url.indexOf("?");
      if (index === -1) return {};

      const queryString = url.substring(index + 1);
      if (!queryString) return {};

      const params = {};

      queryString.split("&").forEach(item => {
        if (!item) return;
        const parts = item.split("=");

        const key = parts[0];
        const value = parts[1] ? decodeURIComponent(parts[1]) : "";

        if (key) params[key] = value;
      });

      return params;
    }
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