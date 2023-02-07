<template>
  <div>
    <h1>message-test</h1>
    <el-button type="primary" @click="login()">登录</el-button>
    <el-button @click="connectSse">连接</el-button>
    <el-button @click="closeSse">关闭连接</el-button>

    <div>message : {{ message }}</div>
  </div>
</template>

<script>
export default {
  name: "testMessage",
  data() {
    return {
      userId: "1614537881802297345",
      message: "",
      source: {},
      token: ''
    };
  },
  mounted() {},
  methods: {
    connectSse() {
      let source;
      console.log(11);

      source = new EventSource(
        "http://localhost:11000/api/yicode-user-openapi/sse/connect?userId=" +
          this.userId
      );
      this.source = source;

      /*
       * 连接一旦建立，就会触发open事件
       * 另一种写法：source.onopen = function (event) {}
       */
      source.onopen =
        ((e) => {
          console.log("e :" + e);
          this.message = "建立连接成功";
        },
        false);

      /**
       * 客户端收到服务器发来的数据
       * 另一种写法：source.onmessage = function (event) {}
       */
      source.onmessage = (e) => {
        console.log("e :" + e);
        this.message = e.data;
      };

      /**
       * 如果发生通信错误（比如连接中断），就会触发error事件
       * 或者：
       * 另一种写法：source.onerror = function (event) {}
       */
      source.onerror =
        ((e) => {
          console.log(e);
          if (e.readyState === EventSource.CLOSED) {
            this.message = "连接关闭";
          }
        },
        false);
    },
    closeSse() {
      this.source.close();
      this.asyncCloseSse().then(() => {
      });
      console.log("close");
    },
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
    async asyncCloseSse() {
      const data = await this.$axios({
        url: "yicode-user-openapi/sse/close?userId=" + this.userId,
        method: "get",
        headers: {
          Authorization:
            "Bearer " + this.token,
            
        },
      });
      console.log(data);
      return data;
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
          username: "王麻子",
          password: "Dailaoyyds123!",
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

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
