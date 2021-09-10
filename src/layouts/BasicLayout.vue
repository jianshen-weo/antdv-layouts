<template>
  <pro-layout
    v-model:collapsed="state.collapsed"
    v-model:selectedKeys="state.selectedKeys"
    v-model:openKeys="state.openKeys"
    :menuData="menuData"
    :fixSiderbar="true"
    layout="mix"
  >
    <template #menuHeaderRender>
      <a>
        <img src="https://alicdn.antdv.com/v2/assets/logo.1ef800a8.svg" />
        <h1>Preview Pro</h1>
      </a>
    </template>
    <template #rightContentRender>
      <div style="margin-right: 12px">
        <a-dropdown>
          <template #overlay>
            <a-menu>
              <a-menu-item>
                <template #icon>
                  <SettingOutlined />
                </template>
                <span>个人设置</span>
              </a-menu-item>
              <a-menu-item>
                <template #icon>
                  <LogoutOutlined />
                </template>
                <span>退出登录</span>
              </a-menu-item>
            </a-menu>
          </template>
          <a-avatar
            shape="square"
            size="small"
          >
            <template #icon>
              <UserOutlined />
            </template>
          </a-avatar>
        </a-dropdown>
      </div>
    </template>
    <!-- 加上这段代码就报错了 -->
    <!-- custom menu-item -->
    <template #menuItemRender="{ item, icon }">
      <a-menu-item
        :key="item.path"
        :disabled="item.meta?.disabled"
        :danger="item.meta?.danger"
        :icon="icon"
      >
        <router-link :to="{ path: item.path }">
          <div class="a-menu-item-title">
            <a-badge count="5" dot>
              {{ item.meta.title }}
            </a-badge>
          </div>
        </router-link>
      </a-menu-item>
    </template>
    <router-view />
  </pro-layout>
</template>

<script lang="ts">
import { defineComponent, reactive, watchEffect } from 'vue'
import { useRouter } from 'vue-router'
import { getMenuData, clearMenuItem } from '@ant-design-vue/pro-layout'
import { Avatar, Dropdown, Menu, Badge } from 'ant-design-vue';
import { UserOutlined, SettingOutlined, LogoutOutlined } from '@ant-design/icons-vue'
import type { RouteContextProps } from '@ant-design-vue/pro-layout'

export default defineComponent({
  components: { 
    UserOutlined,
    SettingOutlined,
    LogoutOutlined,


    [Avatar.name]: Avatar,
    [Dropdown.name]: Dropdown,
    [Menu.name]: Menu,
    [Menu.Item.name]: Menu.Item,
    [Badge.name]: Badge
  },
  setup() {
    const router = useRouter()
    const { menuData } = getMenuData(clearMenuItem(router.getRoutes()))

    const state = reactive<Omit<RouteContextProps, 'menuData'>>({
      collapsed: false,
      openKeys: [],
      selectedKeys: [],
    })

    watchEffect(() => {
      if (router.currentRoute) {
        const matched = router.currentRoute.value.matched.concat()
        state.selectedKeys = matched
          .filter((r) => r.name !== 'index')
          .map((r) => r.path)
        state.openKeys = matched
          .filter((r) => r.path !== router.currentRoute.value.path)
          .map((r) => r.path)
      }
    })
    return {
      state,
      menuData,
    }
  },
})
</script>
