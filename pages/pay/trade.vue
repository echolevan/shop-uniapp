<template>
  <view class="container">
    <view class="price">¥99.00</view>

    <button type="primary" @click="createOrder">
      立即支付
    </button>


  </view>
</template>

<script>
import {
  routineBindingAliWithCode,
} from '@/api/public';
import Routine from '@/libs/routine';

export default {
  data() {
    return {
      tradeNo: ''
    }
  },
  methods: {
    createOrder() {
      Routine.getCode()
          .then(code => {
            console.log(code);
            routineBindingAliWithCode({
              code: code,
            })
                .then(res => {
                  console.log(res);
                  my.checkBeforeAddOrder(
                      {
                        success({requireOrder, sceneId, sourceId}) {
                          console.log(requireOrder, sceneId, sourceId);
                          // 基础库 2.8.13 开始支持 sourceId 和 sceneId
                          if (requireOrder === 1) {
                            // success 为异步回调，请在回调里完成创建订单操作（注意执行顺序，避免在回调执行前进行订单创建），否则有可能造成结果未返回导致使用条件判断错误
                          }
                        },
                        fail({error, errorMessage}) {
                        },
                        complete() {
                        },
                      }
                  )
                })
                .catch(res => {
                  uni.hideLoading();
                });
          })
          .catch(error => {
            uni.hideLoading();
          });

      // this.tradeNo = 'T' + Date.now()
      // this.tradeNo = Date.now()
      // const res = await uni.request({
      //   url: 'https://api.xxx.com/pay/create',
      //   method: 'POST'
      // })

      // if (res.data.code === 0) {
      //   this.tradeNo = res.data.data.trade_no
      // }
    },
  }
}
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
</style>