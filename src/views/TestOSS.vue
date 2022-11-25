<template>
  <div id="app">
    <!-- 上传的组件 -->
    <div>
      <el-upload
        action="https://yicode.oss-cn-chengdu.aliyuncs.com"
        :data="dataObj"
        list-type="picture"
        :multiple="false"
        :show-file-list="showFileList"
        :file-list="fileList"
        :before-upload="beforeUpload"
        :on-remove="handleRemove"
        :on-success="handleUploadSuccess"
        :on-preview="handlePreview"
      >
        <el-button size="small" type="primary">点击上传</el-button>
        <div slot="tip" class="el-upload__tip">
          只能上传jpg/png文件，且不超过10MB
        </div>
      </el-upload>
      <el-dialog :visible.sync="dialogVisible">
        <img width="100%" :src="fileList[0].url" alt="" />
      </el-dialog>
    </div>
    
  </div>
</template>

<script>
export default {
  name: "testOSS",
  data() {
    return {
      dataObj: {
        policy: "",
        signature: "",
        key: "",
        ossaccessKeyId: "",
        dir: "",
        host: "",
        // callback:'',
      },
      dialogVisible: false,
    };
  },
  computed: {
    imageUrl() {
      return this.value;
    },
    imageName() {
      if (this.value != null && this.value !== "") {
        return this.value.substr(this.value.lastIndexOf("/") + 1);
      } else {
        return null;
      }
    },
    fileList() {
      return [
        {
          name: this.imageName,
          url: this.imageUrl,
        },
      ];
    },
    showFileList: {
      get: function () {
        return (
          this.value !== null && this.value !== "" && this.value !== undefined
        );
      },
      set: function (newValue) {
        console.log("newValue : {}", newValue);
      },
    },
  },

  methods: {
    emitInput(val) {
      this.$emit("input", val);
    },
    handleRemove(file, fileList) {
      console.log("file : {}", file);
      console.log("fileList : {}", fileList);
      this.emitInput("");
    },
    handlePreview(file) {
      console.log("file : {}", file);
      this.dialogVisible = true;
    },
    beforeUpload(file) {
      console.log("file : {}", file);
      let _self = this;
      return new Promise((resolve, reject) => {
        new Promise((resolve, reject) => {
          console.log("reject : {}", reject);
          this.$axios({
            url: "/yicode-thirdpart/oss/upload/policy",
            method: "post",
            headers: {
              Authorization:
                "Bearer " +  "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJjb2RlIjoyMDAsInVzZXJfbmFtZSI6InlpeGloYW4iLCJ1c2VyTW9iaWxlIjoiMTc2MjM4NTA0MjYiLCJzY29wZSI6WyJhbGwiXSwiYXRpIjoiZjIxMTQ2YzEtNzJjMi00NzM1LWJjZjUtZGY2ZTZhMGM5YzMzIiwidXNlckVtYWlsIjoiMzExMzc4ODk5N0BxcS5jb20iLCJleHAiOjE2NjkwMTEzNzIsInVzZXJOYW1lIjoieWl4aWhhbiIsInVzZXJJZCI6MSwiYXV0aG9yaXRpZXMiOlsiU1VQRVJfQURNSU4iLCJVU0VSIiwiQURNSU4iXSwianRpIjoiZTY2YzkxY2UtOTE0OS00OTM0LWFkMjctNTUzNmZjNjAyMDhjIiwiY2xpZW50X2lkIjoieWljb2RlIn0.Se14afGczLJvTVmmyUoJCHfm5jY25GNLTK2tEo_tzT_nbHib6Rbshn43DIGDPOINxFDAqNgjDig6g5aooQcD6BKTwT1SMMXKmTF876-icDXrIB6A5C_JPo-4Sjk8WTe8ZEI_svT-G1XCL9yUqNvhE9OZrpPteWW_LbhNm537gQTYbzrV3C-RDcDYzz52pnBKZm01R0X5yAGluCtbAMXsXCe-97m7revAOFwx3ww1k2kmptzn-JYTm_Ack0FG1kg6Oya0DFF21EMJmYkmlnNCQEiTTpxzkXXPiMpq1cO0ZpTbIFo5djIkVjVpRkgW83clEeGc8UWzKX3ljI-3WguNLg",
            },
            data: {
              fileDir: "22-11-16",
              userId: 2,
            },
          }).then(({ data }) => {
            resolve(data);
          });
        })
          .then(({ data }) => {
            console.log("response : ", data);
            _self.dataObj.policy = data.policy;
            _self.dataObj.signature = data.signature;
            _self.dataObj.ossaccessKeyId = data.accessid;
            _self.dataObj.key = data.dir + this.getUUID() + "_${filename}";
            _self.dataObj.dir = data.dir;
            _self.dataObj.host = data.host;
            resolve(true);
          })
          .catch((err) => {
            reject(false);
            console.log("失败", err);
          });
      });
    },
    handleUploadSuccess(res, file) {
      console.log("上传成功...");
      this.showFileList = true;
      this.fileList.pop();
      this.fileList.push({
        name: file.name,
        url:
          this.dataObj.host +
          "/" +
          this.dataObj.key.replace("${filename}", file.name),
      });
      console.log("访问地址:", this.fileList[0].url);
      this.emitInput(this.fileList[0].url);
    },
    //生成uuid
    getUUID() {
      return "xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx".replace(/[xy]/g, (c) => {
        return (
          c === "x" ? (Math.random() * 16) | 0 : "r&0x3" | "0x8"
        ).toString(16);
      });
    }
  },
};
</script>

<style>
</style>