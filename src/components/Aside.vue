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
  <el-menu
    class="page-el-menu"
    mode="vertical"
    :router="true"
    :uniqueOpened="false"
    background-color="#30313300"
    text-color="#fff"
    style="height: 100%"
    :default-active="$route.path"
    @click="handleSelect($event.path[0])"
  >
    <el-scrollbar>
      <Logo></Logo>
      <div class="menu-tab-referrer" ref="menuTabRef"></div>
      <el-menu-item-group>
        <template #title>基础功能</template>
        <el-menu-item key="/overview" index="/overview">
          <i class="el-icon-pie-chart"></i>
          <template #title>数据监控</template>
        </el-menu-item>
        <el-menu-item key="/instances" index="/instances">
          <i class="el-icon-coin"></i>
          <template #title>应用实例</template>
        </el-menu-item>
        <el-menu-item key="/users" index="/users">
          <i class="el-icon-user"></i>
          <template #title>用户管理</template>
        </el-menu-item>
        <!-- <el-menu-item key="/home" index="/home">
          <i class="el-icon-pie-chart"></i>
          <template #title>个人简报</template>
        </el-menu-item> -->
      </el-menu-item-group>
      <el-menu-item-group>
        <template #title>高级功能</template>
        <el-menu-item key="/services" index="/services">
          <i class="el-icon-connection"></i>
          <template #title>守护进程管理</template>
        </el-menu-item>
        <el-menu-item key="/container" index="/container">
          <i class="el-icon-copy-document"></i>
          <template #title>镜像与容器</template>
        </el-menu-item>
        <!-- <el-menu-item key="/update" index="/update">
          <i class="el-icon-guide"></i>
          <template #title>版本控制</template>
        </el-menu-item> -->
      </el-menu-item-group>
      <el-menu-item-group>
        <template #title>扩展功能</template>
        <el-menu-item key="/news" index="/news">
          <i class="el-icon-news"></i>
          <template #title>更新与通知</template>
        </el-menu-item>
      </el-menu-item-group>
      <el-menu-item-group>
        <template #title>更多</template>
        <el-menu-item key="/settings" index="/settings">
          <i class="el-icon-setting"></i>
          <template #title>设置</template>
        </el-menu-item>
      </el-menu-item-group>

      <!-- <el-menu-item-group> -->
      <!-- <el-menu-item index="/extension">
          <i class="el-icon-cpu"></i>
          <template #title>程序接口</template>
        </el-menu-item> -->
      <!-- <el-menu-item index="/trigger">
          <i class="el-icon-document"></i>
          <template #title>触发器</template>
        </el-menu-item> -->
      <!-- <el-menu-item index="/analysis">
          <i class="el-icon-document"></i>
          <template #title>数据分析</template>
        </el-menu-item> -->
      <!-- </el-menu-item-group> -->
    </el-scrollbar>
  </el-menu>
</template>

<script>
import router from "../app/router";
import Logo from "../components/Logo.vue";
import { sleep } from "@/app/service/common";

export default {
  components: { Logo },
  data: function () {
    return { timer: null };
  },
  computed: {
    userInfo() {
      return this.$store.state.userInfo;
    },
    isTopPermission() {
      return this.$store.state.userInfo.permission >= 10;
    },
  },
  beforeUnmount() {

    clearInterval(this.timer);

  },
  created() {

    this.$nextTick(async () => {

      this.timer = setInterval(() => {

        this.handleSelect( document.getElementsByClassName( "el-menu-item is-active" )[0] );

      }, 2000)

    })

  },
  methods: {
    async handleSelect(el) {

      if (!el) return;

      // 判断 el 的class是否有 el-menu-item
      if( el.classList.contains('el-menu-item-group') || !el.classList.contains('el-menu-item') || el.tagName !== "LI" ) {

        return this.handleSelect(el.parentElement);

      }

      const ref = this.$refs.menuTabRef;

      const targetY = el.offsetTop;
      const refY = ref.offsetTop;
      let toHeight;

      if (targetY < refY) {
        toHeight = el.offsetHeight / 2 + refY - targetY + ref.offsetHeight / 2;
        ref.style.top = targetY + "px";
      } else {
        toHeight = ref.offsetHeight / 2 + targetY - refY + el.offsetHeight / 2;
      }

      ref.style.height = toHeight + "px";

      await sleep(350);

      ref.style.top = el.offsetTop + "px";
      ref.style.height = el.offsetHeight + "px"

    },
    toRouter(path) {
      router.push({ path });
    },
  },
};
</script>

<style scoped>
.menu-tab-referrer {
  z-index: 100;
  position: absolute;
  top: 0;
  right: 0;
  width: 3px;
  height: 0;
  background-color: var(--el-color-primary);
  transition: all 0.25s;
}
.logo-wrapper {
  margin: 22px 0px;
  text-align: center;
}
.page-el-menu {
  background: url("../assets/side.png");
  background-size: 1000%;
  background-position-x: 0;
  animation: menuFrame linear infinite 60s;
  transition: all .35s;
}

.page-el-menu:hover {

  background-size: 1020%;
  background-position-y: -5%;

}

@keyframes menuFrame {

  0% {

    background-position-x: 0;

  }

  100% {

    background-position-x: 100%;

  }

}
</style>
