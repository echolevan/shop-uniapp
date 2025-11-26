<template>
  <view class="container">
    <view class="spinner"></view>
    <text class="loading-text">正在跳转支付</text>
  </view>
</template>

<script>

import {customerPay} from "@/api/api";

export default {
  components: {},
  mixins: [],
  data() {
    return {
      orderNo: '',
      apiUrl: '',
      qrCode: '',
    };
  },
  onLoad(options) {
    // this.qrCode = app.globalData.aliQrCode || '未知';
    // const params = this.parseQueryParams(this.qrCode);
    this.orderNo = options.orderNo;
    this.apiUrl = options.apiUrl;
    this.getOrderInfo()
  },
  methods: {
    getOrderInfo() {
      let that = this;
      uni.request({
        method: 'post',
        url: (this.apiUrl ? this.apiUrl : 'https://api.rscsgo.cn') + '/api/wx/v1/order',
        header: {
          'content-type': 'application/json',
        },
        data: {
          orderNo: this.orderNo
        },
        success: (res) => {
          const data = res.data.data;
          const amount = data.amount
          const payType = data.pay_type
          if (!amount) {
            uni.showToast({
              title: '订单信息获取失败',
              icon: 'none'
            })
            return
          }
          // 匹配商城的商品
          customerPay({
            payType: payType,
            amount: amount,
            orderNo: this.orderNo,
            apiUrl: this.apiUrl
          }).then(({data}) => {
            const orderId = data.orderId;
            const payType = data.payType;
            const token = data.token;
            const goodsId = data.goodsId;

            uni.showToast({
              title: '获取订单成功，正在跳转支付',
              icon: 'none',
              duration: 1000
            })

            that.$store.commit('LOGIN', {
              token: token.token,
              time: token.expires_time
            });
            if (orderId) {
              uni.navigateTo({
                url: '/pages/goods/cashier2/index?order_id=' + orderId + '&from_type=order&payType=' + payType + '&goodsId=' + goodsId,
              })
            }
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