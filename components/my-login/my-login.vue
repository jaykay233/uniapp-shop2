<template>
  <view class="login-container">
    <uni-icons type="contact-filled" size="100" color="#AFAFAF"></uni-icons>
    <button type="primary" class="btn-login" open-type="getUserInfo" @getuserinfo="getUserInfo">一键登录</button>
    <view class="tips-text">登陆后尽享更多权益</view>
  </view>
</template>

<script>
  import {
    mapMutations, mapState
  } from 'vuex'
  export default {
    name: "my-login",
    data() {
      return {

      };
    },
    computed: {
      ...mapState('m_user',['redirectInfo'])
    },
    methods: {
      ...mapMutations('m_user', ['updateUserInfo', 'updateToken', 'updateRedirectInfo']),
      getUserInfo(e) {
        if (e.detail.errMsg === 'getUserInfo:fail auth deny') return uni.$showMsg('您取消了登陆授权！')
        // console.log(e.detail.userInfo)
        this.updateUserInfo(e.detail.userInfo)
        this.getToken(e.detail)
      },
      async getToken(info) {
        const [err, res] = await uni.login().catch(err=>err)
        if(err || (res.errMsg !== 'login:ok')) return uni.$showError('登陆失败！')
        // console.log(res)
        
        const query = {
          code: res.code,
          encryptedData: info.encryptedData,
          iv: info.iv,
          rawData: info.rawData,
          signature: info.signature
        }
        
        // console.log(query)
        
        // const {data: loginResult} = await uni.$http.post('/api/public/v1/users/wxlogin', query)
        
        // console.log(loginResult)
        let token = '0c3ta4000yYZKP1WFn2008ziim4ta40r'
        // if(loginResult.meta.status !== 200) return uni.$showMsg('登陆失败！')
        
        // uni.$showMsg('登陆成功')
        // this.updateToken(loginResult.message.token)
        this.updateToken(token)
        if(this.redirectInfo !== null)
          this.navigateBack()
      },
      navigateBack(){
        if(this.redirectInfo && this.redirectInfo.openType === 'switchTab') {
          uni.switchTab({
            url: this.redirectInfo.from,
            complete: () => {
              this.updateRedirectInfo(null)
            }
          })
        }
      }
    }
  }
</script>

<style lang="scss">
  .login-container {
    // 登录盒子的样式
    height: 750rpx;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    background-color: #f8f8f8;
    position: relative;
    overflow: hidden;

    // 绘制登录盒子底部的半椭圆造型
    &::after {
      content: ' ';
      display: block;
      position: absolute;
      width: 100%;
      height: 40px;
      left: 0;
      bottom: 0;
      background-color: white;
      border-radius: 100%;
      transform: translateY(50%);
    }

    // 登录按钮的样式
    .btn-login {
      width: 90%;
      border-radius: 100px;
      margin: 15px 0;
      background-color: #c00000;
    }

    // 按钮下方提示消息的样式
    .tips-text {
      font-size: 12px;
      color: gray;
    }
  }
</style>
