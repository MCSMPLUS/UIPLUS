<!--
  Copyright (C) 2022 Suwings <Suwings@outlook.com>

  This program is free software: you can redistribute it and/or modify
  it under the terms of the GNU Affero General Public License as published by
  the Free Software Foundation, either version 3 of the License, or
  (at your option) any later version.

  According to the AGPL, it is forbidden to delete all copyright notices,
  and if you modify the source code, you must open source the
  modified source code.

  版权所有 (C) 2022 Suwings <Suwings@outlook.com>

  该程序是免费软件，您可以重新分发和/或修改据 GNU Affero 通用公共许可证的条款，
  由自由软件基金会，许可证的第 3 版，或（由您选择）任何更高版本。

  根据 AGPL 与用户协议，您必须保留所有版权声明，如果修改源代码则必须开源修改后的源代码。
  可以前往 https://mcsmanager.com/ 阅读用户协议，申请闭源开发授权等。
-->

<template>
  <div v-if="isTopPermission" class="header-container">

    <div class="header-left">
      <!-- 手机端只显示扩展按钮 -->
      <div class="only-phone-display header-left-buttion" @click="toAside">
        <i class="el-icon-s-operation"></i>
      </div>
      <!-- 面包屑-->
      <div
              style="font-size: 14px; font-weight: 700; line-height: 28px"
              v-for="(item, index) in breadcrumbsList"
              :to="{ path: item.path }"
              :key="index"
      >
        <span class="only-pc-display">控制面板 / {{ item.title }}</span>
      </div>

    </div>
    <div class="header-right">
      <el-popover style="margin: 0px 5px"
                  trigger="hover"
      >
        <template #default>
          <el-menu class="header-avatar-menu">
            <el-menu-item @click="toPrivate" index="1" class="hover-for-blue">
              <el-icon><user /></el-icon>个人资料
            </el-menu-item>
            <el-menu-item @click="logout" index="2" class="hover-for-red">
              <el-icon><bottom-left /></el-icon>退出
            </el-menu-item>
          </el-menu>
        </template>
        <template #reference>
          <div class="el-dropdown-avatar">
            <span class="avatar-user">{{ userInfo.userName }}</span>
          </div>
        </template>
      </el-popover>
    </div>

  </div>

  <div v-if="!isTopPermission" class="header-container">
    <div class="header-left">
      <router-link to="/home">
        <div style="height: 36px; line-height: 36px">
          <div>
            <Logo style="vertical-align: text-top" dark margin="0px"></Logo>
          </div>
        </div>
      </router-link>
    </div>
    <div class="header-right">
      <el-popover style="margin: 0px 5px"
                  trigger="hover"
      >
        <template #default>
          <el-menu class="header-avatar-menu">
            <el-menu-item @click="toPrivate" index="1" class="hover-for-blue">
              <el-icon><user /></el-icon>个人资料
            </el-menu-item>
            <el-menu-item @click="logout" index="2" class="hover-for-red">
              <el-icon><bottom-left /></el-icon>退出
            </el-menu-item>
          </el-menu>
        </template>
        <template #reference>
          <div class="el-dropdown-avatar">
            <span class="avatar-user">{{ userInfo.userName }}</span>
          </div>
        </template>
      </el-popover>
    </div>
  </div>
</template>

<script>
import router from "../app/router";
import { API_USER_LOGOUT } from "@/app/service/common";
import { request } from "@/app/service/protocol";
import Logo from "./Logo.vue";
import { User, BottomLeft } from '@element-plus/icons'

export default {
  props: {
    breadcrumbsList: Array,
    breadcrumbs: String,
    aside: Function
  },
  computed: {
    userInfo() {
      return this.$store.state.userInfo;
    },
    isTopPermission() {
      return this.$store.state.userInfo.permission >= 10;
    }
  },
  methods: {
    toAside() {
      this.$props.aside();
    },
    toPrivate() {
      router.push({ path: "/private" });
    },
    async logout() {
      try {
        await request({
          method: "GET",
          url: API_USER_LOGOUT
        });
        window.location.href = "/";
        this.$notify({
          title: "退出成功",
          message: "欢迎下次使用",
          type: "success"
        });
      } catch (error) {
        this.$notify({
          title: "退出失败",
          message: error.message,
          type: "error",
          duration: 0
        });
      }
    }
  },
  components: { Logo, User, BottomLeft }
};
</script>

<style>

.header-avatar-menu .el-menu-item i {

  position: relative;

  margin-right: 5px;
  top: -2px;

}

.header-avatar-menu .el-menu-item:hover {

  background-color: rgba(0, 0, 0, 0.16) !important;
  border: 1px solid rgba(0,0,0,0.16);
  border-radius: 4px;
  backdrop-filter: saturate(180%) blur(20px);

}

</style>

<style scoped>
.header-container {

  padding: 10px 25px 0 20px;

  display: flex;
  justify-content: space-between;

}

.header-left {

  float: left;

}

.header-right {

  float: right;

}

.el-dropdown-avatar .avatar-user {

  position: relative;

  left: -150%;
  top: 26%;

  font-size: 1.025rem;
  font-weight: 500;

  transition: all .25s;

}

.el-dropdown-avatar:hover .avatar-user {

  opacity: .75;

}

.el-dropdown-avatar {

  margin: -4px;
  padding: 2px;

  width: 30px;
  height: 30px;

  cursor: pointer;
  color: #409eff;
  border: 2px solid #8e7f7a;

  background-image: url('./../assets/avatar.png');
  background-repeat: no-repeat;
  background-size: cover;
  box-shadow: 0 5px 8px 0 rgb(0 0 0 / 12%);
  border-radius: 3px;

}
.el-icon-arrow-down {
  font-size: 12px;
}

.header-left-buttion {
  line-height: 28px;
  font-size: 22px;
  cursor: pointer;
}

.header-a {
  color: rgb(235, 235, 235);
  font-weight: 400;
}
</style>
