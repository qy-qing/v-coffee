<template>
  <div class="account">
    <van-nav-bar title="个人资料" left-text="返回" left-arrow fixed @click-left="back" />
    <BgBox>
      <van-cell title="头像" :center="true">
        <div>
          <div class="user-img fr">
            <img class="auto-img" :src="accountInfo.userImg" alt="" />
            <van-uploader class="upload-box" :after-read="uploadUserImg" />
          </div>
        </div>
      </van-cell>
      <van-cell title="用户id" :center="true">{{accountInfo.userId}}</van-cell>
      <van-cell title="手机号" :center="true">{{accountInfo.phone}}</van-cell>
      <van-cell title="昵称" :center="true">
        <van-field v-model="accountInfo.nickName" class="field-box" placeholder="输入昵称" maxlength="12"
          @change="updateNickName" />
      </van-cell>
      <div class="desc-box">
        <div class="desc-content">
          <van-field v-model="accountInfo.desc" rows="2" autosize label="简介" type="textarea" maxlength="30"
            placeholder="输入简介" show-word-limit input-align="right" @change="updateDesc" />
        </div>
      </div>
    </BgBox>
  </div>
</template>

<script>
  import "../assets/less/account.less";
  import BgBox from "../components/BgBox.vue";
  export default {
    name: "Account",
    components: {
      BgBox,
    },
    data() {
      return {
        accountInfo: {
          nickName: '',
          desc: '',
          phone: '',
          userId: '',
          desc: ''
        },
      };
    },
    created() {
      //获取个人资料
      this.getAccountInfo();
    },
    methods: {
      back() {
        this.$router.go(-1);
      },

      //获取个人资料
      getAccountInfo() {
        let tokenString = localStorage.getItem("__tk");

        if (!tokenString) {
          //跳回登录页面
          this.$toast("请先登录");
          return this.$router.push({
            name: "Login"
          });
        }

        this.$toast.loading({
          message: "加载中...",
          forbidClick: true,
          duration: 0,
        });

        this.axios({
            method: "GET",
            url: "/findAccountInfo",
            params: {
              appkey: this.appkey,
              tokenString,
            },
          })
          .then((result) => {
            this.$toast.clear();

            if (result.data.code == 700) {
              //token检验无效,则跳到登录页面
              this.$router.push({
                name: "Login"
              });
            } else if (result.data.code == "B001") {
              let data = result.data.result[0];
              if (data.desc == '') {
                data.desc = '这家伙很懒，什么都没有留下！'
              }
              this.accountInfo = data;
            }
          })
          .catch((err) => {
            this.$toast.clear();

          });
      },

      //上传用户头像
      uploadUserImg(file) {
        // 
        //允许文件类型
        let type = ['gif', 'png', 'jpg', 'jpeg'];

        //允许最大文件大小 1MB
        let size = 1;

        //判断文件类型
        let fileType = file.file.type.split('/')[1];
        if (type.indexOf(fileType) === -1) {
          this.$toast(`文件类型只支持${type.join(',')}`);
          return;
        }

        //判断文件大小 B, 1024B = 1KB, 1024KB = 1MB
        let fileSize = file.file.size / 1024 / 1024;

        if (fileSize > size) {
          this.$toast(`文件允许最大不能超过${size}MB`);
          return;
        }

        //处理base64的标记data:image/jpeg;base64,
        let base64 = file.content.replace(/^data:image\/[A-Za-z]+;base64,/, '');
        // 

        //发起请求
        let tokenString = localStorage.getItem("__tk");

        if (!tokenString) {
          //跳回登录页面
          this.$toast("请先登录");
          return this.$router.push({
            name: "Login"
          });
        }

        this.$toast.loading({
          message: "加载中...",
          forbidClick: true,
          duration: 0,
        });

        this.axios({
            method: "POST",
            url: "/updateAvatar",
            data: {
              appkey: this.appkey,
              tokenString,
              imgType: fileType,
              serverBase64Img: base64
            },
          })
          .then((result) => {
            this.$toast.clear();

            if (result.data.code == 700) {
              //token检验无效,则跳到登录页面
              this.$router.push({
                name: "Login"
              });
            } else if (result.data.code == 'H001') {
              this.accountInfo.userImg = result.data.userImg;
            }

            this.$toast(result.data.msg);

          })
          .catch((err) => {
            this.$toast.clear();

          });
      },

      //修改昵称
      updateNickName() {

        if (!this.accountInfo.nickName) {
          this.$toast('昵称不能为空');
        }

        let tokenString = localStorage.getItem("__tk");

        if (!tokenString) {
          //跳回登录页面
          this.$toast("请先登录");
          return this.$router.push({
            name: "Login"
          });
        }

        this.$toast.loading({
          message: "加载中...",
          forbidClick: true,
          duration: 0,
        });

        this.axios({
            method: "POST",
            url: "/updateNickName",
            data: {
              appkey: this.appkey,
              tokenString,
              nickName: this.accountInfo.nickName
            },
          })
          .then((result) => {
            this.$toast.clear();

            if (result.data.code == 700) {
              //token检验无效,则跳到登录页面
              this.$router.push({
                name: "Login"
              });
            } else {
              this.$toast(result.data.msg);
            }

          })
          .catch((err) => {
            this.$toast.clear();

          });
      },

      //修改简介
      updateDesc() {
        if (!this.accountInfo.nickName) {
          this.$toast('昵称不能为空');
        }

        let tokenString = localStorage.getItem("__tk");

        if (!tokenString) {
          //跳回登录页面
          this.$toast("请先登录");
          return this.$router.push({
            name: "Login"
          });
        }

        this.$toast.loading({
          message: "加载中...",
          forbidClick: true,
          duration: 0,
        });

        this.axios({
            method: "POST",
            url: "/updateDesc",
            data: {
              appkey: this.appkey,
              tokenString,
              desc: this.accountInfo.desc
            },
          })
          .then((result) => {
            this.$toast.clear();

            if (result.data.code == 700) {
              //token检验无效,则跳到登录页面
              this.$router.push({
                name: "Login"
              });
            } else {
              this.$toast(result.data.msg);
            }

          })
          .catch((err) => {
            this.$toast.clear();

          });
      }
    },
  };
</script>

<style lang="less" scoped>
</style>