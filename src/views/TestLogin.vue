<template>
  <div>
    <el-button type="primary" @click="login()">登录</el-button>
    <el-button type="primary" @click="getLoginUserMessage()">获取用户信息</el-button>
    <div>
      {{ userDetailInfo }}
    </div>
  </div>
</template>

<script>
export default {
  name: "testLogin",
  data() {
    return {
      token: '',
      userDetailInfo : {}
    }
  },
  methods: {
    login() {
      this.asyncLogin().then(({ data }) => {
        console.log("userinfo", data);
        this.token = data.access_token;
        console.log("token,",this.token);
      });
    },
    
    getLoginUserMessage () {
      this.asyncgetLoginUserMessage().then(({ data }) => {
        console.log("userInfo", data.data);
        this.userDetailInfo = data.data

      })
    },
    async asyncgetLoginUserMessage() {
      const data = await this.$axios({
        url: "/yicode-user-openapi//open/user/now",
        method: "get",
        headers: {
          Authorization: "Bearer " + this.token
        }
      });
      console.log(data);
      return data;
    },

    async asyncLogin() {
      const data = await this.$axios({
        url: "/yicode-auth/oauth/token",
        method: "post",
        headers: {
          "Content-Type": "application/x-www-form-urlencoded",
        },
        data: {
          grant_type: "password",
          username: "yixihan",
          password: "Theyear123.",
          scope: "all",
          client_id: "yicode",
          client_secret: "yicode",
        },
      });
      console.log(data);
      return data;
    },
  },
};
</script>

<style>
</style>