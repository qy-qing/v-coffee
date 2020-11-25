<template>
  <div class="secure">
    <van-nav-bar
      title="安全中心"
      left-text="返回"
      left-arrow
      fixed
      @click-left="back"
    />
    <BgBox>
      <div class="cell-box">
        <van-cell
          title="修改密码"
          is-link
          :center="true"
          @click="openPasswordBox"
        />
        <van-cell
          title="注销账号"
          is-link
          :center="true"
          @click="destroyAccount"
        />
      </div>
    </BgBox>

    <div class="logout-box">
      <van-button color="#0caaba" block round @click="logout"
        >退出登录</van-button
      >
    </div>

    <van-popup is-link position="bottom" closeable v-model="isOpen">
      <div class="form-box">
        <div class="form-title">修改密码</div>
        <van-form>
          <van-field
            v-model="password.oldPassword"
            :type="isPassword ? 'password' : 'text'"
            name="旧密码"
            label="旧密码"
            placeholder="旧密码(首字符必须为字母)"
            :right-icon="isPassword ? 'closed-eye' : 'eye-o'"
            autocomplete="off"
            @click-right-icon="toggleType"
          />

          <van-field
            v-model="password.newPassword"
            :type="isPassword ? 'password' : 'text'"
            name="新密码"
            label="新密码"
            placeholder="新密码(首字符必须为字母)"
            :right-icon="isPassword ? 'closed-eye' : 'eye-o'"
            autocomplete="off"
            @click-right-icon="toggleType"
          />

          <div class="commit-btn">
            <van-button round block color="#0caaba" @click="commit"
              >提交</van-button
            >
          </div>
        </van-form>
      </div>
    </van-popup>
  </div>
</template>

<script>
import "../assets/less/secure.less";
import BgBox from "../components/BgBox.vue";

//导入表单验证模块
import { validForm } from "../assets/js/validForm";

export default {
  name: "Secure",
  components: {
    BgBox,
  },
  data() {
    return {
      isOpen: false,
      isPassword: true,
      password: {
        oldPassword: "",
        newPassword: "",
      },
    };
  },
  methods: {
    back() {
      this.$router.go(-1);
    },
    toggleType() {
      this.isPassword = !this.isPassword;
    },
    openPasswordBox() {
      this.isOpen = true;
    },

    //注销账号
    destroyAccount() {
      this.$dialog
        .confirm({
          title: "注销账号",
          message: "是否确定注销账号，一旦注销无法恢复！",
          confirmButtonColor: "#0C34BA",
        })
        .then(() => {
          //执行账号注销
          

          //发起修改密码请求
          let tokenString = localStorage.getItem("__tk");

          if (!tokenString) {
            //跳回登录页面
            this.$toast("请先登录");
            return this.$router.push({ name: "Login" });
          }

          this.$toast.loading({
            message: "加载中...",
            forbidClick: true,
            duration: 0,
          });

          this.axios({
            method: "POST",
            url: "/destroyAccount",
            data: {
              appkey: this.appkey,
              tokenString,
            },
          })
            .then((result) => {
              this.$toast.clear();
              
              if (result.data.code == 700) {
                //token检验无效,则跳到登录页面
                this.$router.push({ name: "Login" });
              } else if (result.data.code == "G001") {
                setTimeout(() => {
                  //清除登录状态
                  localStorage.removeItem("__tk");
                  this.$router.push({ name: "Login" });
                }, 800);
              }

              this.$toast(result.data.msg);
            })
            .catch((err) => {
              this.$toast.clear();
              
            });
        })
        .catch((err) => {
          
        });
    },

    //退出登录
    logout() {
      this.$dialog
        .confirm({
          title: "退出登录",
          message: "是否确定退出登录！",
          confirmButtonColor: "#0C34BA",
        })
        .then(() => {
          //执行退出登录
          //清除登录状态
          this.$toast.loading({
            message: "加载中...",
            forbidClick: true,
            duration: 0,
          });

          setTimeout(() => {
            this.$toast.clear();
            localStorage.removeItem("__tk");
            this.$router.push({ name: "Login" });
          }, 800);
        })
        .catch((err) => {
          
        });
    },

    //提交修改密码
    commit() {
      //构造表单验证信息
      let o = {
        oldPassword: {
          value: this.password.oldPassword,
          errorMsg: "旧密码由数字字母下划线组合(6-16字符)",
          reg: /^[A-Za-z]\w{5,15}$/,
        },
        newPassword: {
          value: this.password.newPassword,
          errorMsg: "新密码由数字字母下划线组合(6-16字符)",
          reg: /^[A-Za-z]\w{5,15}$/,
        },
      };

      let isPass = validForm.valid(o);

      //如果表单验证通过
      if (isPass) {
        if (this.password.newPassword == this.password.oldPassword) {
          this.$toast("新密码和旧密码不能相同");
          return;
        }

        //发起修改密码请求
        let tokenString = localStorage.getItem("__tk");

        if (!tokenString) {
          //跳回登录页面
          this.$toast("请先登录");
          return this.$router.push({ name: "Login" });
        }

        this.$toast.loading({
          message: "加载中...",
          forbidClick: true,
          duration: 0,
        });

        this.axios({
          method: "POST",
          url: "/updatePassword",
          data: {
            appkey: this.appkey,
            tokenString,
            password: this.password.newPassword,
            oldPassword: this.password.oldPassword,
          },
        })
          .then((result) => {
            this.$toast.clear();
            
            if (result.data.code == 700) {
              //token检验无效,则跳到登录页面
              this.$router.push({ name: "Login" });
            } else if (result.data.code == "E001") {
              setTimeout(() => {
                //清除登录状态
                localStorage.removeItem("__tk");
                this.$router.push({ name: "Login" });
              }, 800);
            }

            this.$toast(result.data.msg);
          })
          .catch((err) => {
            this.$toast.clear();
            
          });
      }
    },
  },
};
</script>

<style lang="less" scoped>
</style>