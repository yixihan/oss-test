<template>
  <div>
    <el-button type="primary" @click="login()">登录</el-button>
  </div>
</template>

<script>
export default {
  name: "testLogin",
  methods: {
    login() {
      this.asyncLogin().then(({ data }) => {
        console.log("userinfo", data);
      });
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
          password: "123456",
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