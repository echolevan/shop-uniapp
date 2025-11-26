<template>
  <view class="container">
    <view class="success-box">
      <image class="success-icon" src="../../static/images/paySuccess.png" mode="widthFix"></image>
      <view class="text-success">支付成功</view>
    </view>
  </view>

</template>

<script>

export default {
  components: {},
  mixins: [],
  data() {
  },
  onLoad() {
  },
  // 禁止返回（支付宝小程序）
  onBackPress() {
    return true;
  },
  methods: {
  }
};
</script>

<style>
page {
  background-color: #fff;
}
</style>

<style scoped>
.container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 80vh;
  background-color: #ffffff;
}

.success-box {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.success-icon {
  width: 180rpx;
  height: 180rpx;
}

.icon-check {
  font-size: 120rpx;
  color: #07c160; /* 微信绿 */
}

.text-success {
  margin-top: 30rpx;
  font-size: 48rpx;
  color: #333;
  font-weight: bold;
}
</style>
