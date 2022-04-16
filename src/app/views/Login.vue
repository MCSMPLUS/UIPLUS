<!--
  Copyright (C) 2022 Suwings <Suwings@outlook.com> TalexDreamSoul <TalexDreamSoul@Gmail.com>

  This program is free software: you can redistribute it and/or modify
  it under the terms of the GNU Affero General Public License as published by
  the Free Software Foundation, either version 3 of the License, or
  (at your option) any later version.

  According to the AGPL, it is forbidden to delete all copyright notices,
  and if you modify the source code, you must open source the
  modified source code.

  版权所有 (C) 2022 Suwings <Suwings@outlook.com> TalexDreamSoul <TalexDreamSoul@Gmail.com>

  该程序是免费软件，您可以重新分发和/或修改据 GNU Affero 通用公共许可证的条款，
  由自由软件基金会，许可证的第 3 版，或（由您选择）任何更高版本。

  根据 AGPL 与用户协议，您必须保留所有版权声明，如果修改源代码则必须开源修改后的源代码。
  可以前往 https://mcsmanager.com/ 阅读用户协议，申请闭源开发授权等。
-->

<template>
  <div
    id="login-layer-top"
    :class="{ 'login-layer-fadeout-top': close, 'login-layer-fadein-top': !close }"
  ></div>
  <div
    id="login-layer-left"
    :class="{ 'login-layer-fadeout-left': close, 'login-layer-fadein-left': !close }"
  ></div>
  <div
    id="login-layer-right"
    :class="{ 'login-layer-fadeout-right': close, 'login-layer-fadein-right': !close }"
  ></div>
  <div
    id="login-layer-bottom"
    :class="{ 'login-layer-fadeout-bottom': close, 'login-layer-fadein-bottom': !close }"
  ></div>

  <div id="login-panel-wrapper" :class="{ 'login-panel-wrapper-out': closeWindow }">

    <Panel id="login-panel" body-style="padding:44px;" v-loading="loading">
      <template #default>
        <div :class="{ 'bar-loading': loading, 'bar-error': error, 'bar-warning': cause }" class="login-panel-mention-bar"></div>
        <form action="/login" method="post">
          <div style="text-align: center;font-size: 24px; font-weight: 600"><span class='title'>身份验证</span></div>
          <p style='text-align: center'>使用 <span class='mcsm-plus'>MCSM<span class='mcsm-plus-plus'>+</span></span> 账号进行登录</p>
          <form action="/" method="post">
            <div style="margin-top: 22px">
              <div>
                <el-input
                  type="text"
                  name="mcsm_username"
                  v-model="form.username"
                  placeholder="账号"
                  autocomplete="on"
                  :disabled="close"
                  @keyup.enter="submit"
                >
                  <template #prefix>
                    <i class="el-icon-user"></i>
                  </template>
                </el-input>
              </div>
              <div class="row-mt">
                <el-input
                  type="password"
                  name="mcsm_password"
                  v-model="form.password"
                  placeholder="密码"
                  autocomplete="on"
                  :disabled="close"
                  @keyup.enter="submit"
                >
                  <template #prefix>
                    <i class="el-icon-lock"></i>
                  </template>
                </el-input>
              </div>
              <div class="login-btn-wrapper row-mt">
                <transition name="fade">
                  <div v-if="cause" id="login-cause">{{ cause }}</div>
                </transition>
                <el-button
                  type="primary"
                  size="small"
                  style="width: 110px"
                  @click="login"
                  :disabled="close"
                  :loading="loading"
                >
                  {{ loginText }}
                </el-button>
              </div>
              <div class="login-info-wrapper row-mt">
                <div>
                  <span class="color-gray"
                    > © 2022
                    <a target="black" href="https://github.com/MCSMPLUS">MCSM+</a></span
                  >
                </div>
              </div>
            </div>
          </form>
        </form>
      </template>
    </Panel>
  </div>

  <div>
    <el-row :gutter="20">
      <el-col :md="24" :offset="0">
        <Panel>
          <template #default>
            <el-skeleton :rows="8" />
          </template>
        </Panel>
        <Panel>
          <template #default>
            <el-skeleton :rows="2" />
          </template>
        </Panel>
        <Panel>
          <template #default>
            <el-skeleton :rows="2" />
          </template>
        </Panel>
      </el-col>
    </el-row>
  </div>
</template>

<script>
import Panel from "../../components/Panel";
// eslint-disable-next-line no-unused-vars
// import router from "../router";
import { API_USER_LOGIN, sleep } from "../service/common";
import { request, setupUserInfo } from "../service/protocol";

export default {
  components: { Panel },
  data: function () {
    return {
      form: {
        username: "",
        password: ""
      },
      close: false,
      closeWindow: false,
      loginText: "登录",
      loading: false,
      cause: "",
      error: false,
    };
  },
  methods: {
    // 回车登录
    submit() {
      this.login();
    },
    // 登录过程入口
    async login() {
      try {
        if (!this.form.username || !this.form.username) {
          throw new Error("账号或密码不能为空值");
        }
        this.loading = true;
        this.error = false;
        this.cause = "";
        this.loginText = "登录中";
        await sleep(2200);
        const res = await request({
          method: "POST",
          url: API_USER_LOGIN,
          data: {
            username: this.form.username,
            password: this.form.password
          }
        });
        if (res) {
          return this.success(res);
        }
      } catch (error) {
        await this.failed( error );
      } finally {
        this.loading = false;
      }
    },
    // 登录失败
    async failed(error) {
      this.cause = error.message;
      if (this.cause === "null") {
        this.error = true;
        this.cause = "账号或密码错误，请检查后重试";
      }
      this.loginText = "重新登录";
      this.close = true;
      await sleep(700);
      this.close = false;
    },
    // 登录成功
    async success() {
      this.close = true;
      this.closeWindow = true;
      this.loginText = "登录成功";
      try {
        const info = await setupUserInfo();
        const func = async () => {
            if( !info ) {
              setTimeout(() => {
                func()
              }, 200)
            } else {
              await this.$router.push( info.permission === 1 ? '/home' : '/overview' );
            }
        }
        await func()
      } catch (error) {
        this.$notify({
          title: "网页无法正确运作",
          message: "无法获取身份数据，网页所有功能将全部不可用，请立刻刷新网页或重新登录",
          type: "error",
          duration: 0
        });
      }
      // window.location.href = "/";
    }
  },
  async mounted() {
    console.log("Welcome use MCSM+.");
    console.log("Copyright 2022 MCSMPLUS All rights reserved.");
    // try {
    //   await setupUserInfo();
    //   if (this.$store.state?.userInfo?.uuid) {
    //     console.log("用户已登录，正在跳转");
    //     window.location.href = "/";
    //   }
    // } catch (err) {
    //   // 忽略
    // }
  }
};
</script>

<style scoped>

.login-panel-mention-bar {

  z-index: 10000;
  position: absolute;

  top: 0;
  left: 0;

  width: 100%;
  height: 2px;

  background-color: var(--el-color-primary);
  transition: all .35s

}

.bar-error {

  animation: barHeightChanger 2.75s;

  background-color: var(--el-color-error)

}

.bar-warning {

  animation: barHeightChanger 2.75s;

  background-color: var(--el-color-warning)

}

@keyframes barHeightChanger {

  0% {

    height: 4px;

  }

  100% { height: 2px; }

}

.bar-loading {

  animation: loadingBar 1.45s linear infinite;

}

@keyframes loadingBar {

  0% {

    left: 0;
    width: 0;

  }

  20% {

    left: 0;
    width: 50%;

  }

  40% {

    left: 25%;
    width: 75%;

  }

  60% {

    left: 50%;
    width: 50%;

  }

  80% {

    left: 75%;
    width: 25%;

  }

  100% {

    left: 100%;
    width: 0%;

  }

}

.title {

  position: relative;

  width: 100%;

}

.title::before {

  content: "";
  position: absolute;

  left: 0;
  bottom: 1px;

  height: 5px;
  width: 100%;

  border-radius: 4px;
  background-color: var(--el-color-primary);

  opacity: .75;

}

/* 动画与基本元素CSS */

.login-panel-wrapper-out {
  opacity: 0;
  z-index: 1;
}

.login-layer-fadeout-top {
  top: 0px;
  left: 0px;
  right: 0px;
  bottom: 100vh;
  opacity: 0.2;
}

.login-layer-fadein-top {
  bottom: 50vh;
}

.login-layer-fadeout-bottom {
  top: 100vh;
  left: 0px;
  right: 0px;
  bottom: 0px;
  opacity: 0.2;
}

.login-layer-fadein-bottom {
  top: 50vh;
}

.login-layer-fadeout-left {
  top: 0px;
  left: 100vw;
  right: 0px;
  bottom: 100vh;
  opacity: 0.2;
}

.login-layer-fadein-left {
  left: 50vw;
}

.login-layer-fadeout-right {
  top: 0px;
  right: 100vw;
  left: 0px;
  bottom: 100vh;
  opacity: 0.2;
}

.login-layer-fadein-right {
  right: 50vw;
}

#login-layer-top,
#login-layer-bottom {
  transition-property: all;
  transition-duration: 1.6s;
  transition-timing-function: cubic-bezier(0.17, 0.99, 0.63, 0.6);
  transition-delay: 0s;
}

#login-layer-right,
#login-layer-left {
  transition-property: all;
  transition-duration: 0.5s;
  transition-timing-function: cubic-bezier(0.17, 0.99, 0.63, 0.6);
  transition-delay: 0s;
}

#login-layer-top,
#login-layer-left,
#login-layer-right,
#login-layer-bottom {
  z-index: 998;
  background-color: rgb(228, 228, 228);
  position: fixed;
}
#login-layer-top {
  top: 0px;
  left: 0px;
  right: 0px;
}

#login-layer-bottom {
  bottom: 0px;
  left: 0px;
  right: 0px;
}
#login-layer-left {
  top: 0px;
  right: 0px;
  bottom: 0px;
}
#login-layer-right {
  top: 0px;
  left: 0px;
  bottom: 0px;
}

#login-panel-wrapper {
  position: fixed;
  z-index: 999;
  top: 0px;
  left: 0px;
  right: 0px;
  bottom: 0px;

  display: flex;
  align-items: center;

  transition-property: all;
  transition-duration: 1.5s;
  transition-timing-function: cubic-bezier(1, 0.05, 0.84, 0.74);
  transition-delay: 0s;
}

#login-panel {
  position: absolute;
  min-height: 330px;
  width: 430px;

  left: 50%;
  top: 50%;

  transition: all .35s;
  transform: translate(-50%, -50%);
}

#login-panel:hover {
  border: 1px solid #77797c;
}

.login-btn-wrapper {
  display: flex;
  justify-content: flex-end;
  align-items: center;
}

.login-info-wrapper {
  display: flex;
  justify-content: flex-end;
  align-items: center;
  text-align: right;
}

.login-info-wrapper a {
  color: gray;
  text-decoration: underline;
}

#login-cause {
  color: rgb(170, 8, 8);
  font-size: 12px;
  margin-right: 18px;
}

/* 针对手机的登录界面 */
@media (max-width: 900px) {
  #login-panel {
    text-align: center;
    margin: 0;
    padding: 0;
    min-height: 100vh;
    height: 100vh;
    width: 100vw;
    display: flex;
    justify-content: space-around;
    align-items: center;
    border-radius: 0px;
  }
  #login-panel:hover {
    border: none;
  }
  .login-btn-wrapper {
    display: flex;
    justify-content: flex-start;
    align-items: center;
    flex-direction: column-reverse;
    text-align: center;
  }
  #login-cause {
    margin-top: 12px;
    margin-right: 0px;
  }
  .login-info-wrapper {
    text-align: center;
    display: flex;
    justify-content: space-around;
    align-items: center;
  }
}
</style>
