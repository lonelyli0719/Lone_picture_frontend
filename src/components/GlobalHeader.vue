<template>
  <div id="globalHeader">
    <a-row :wrap="false">
      <a-col flex="200px">
        <router-link to="/">
          <div class="title-bar">
            <img class="logo" src="../assets/logo.png" alt="logo"/>
            <div class="title">Lone皮云图库</div>
          </div>
        </router-link>
      </a-col>
      <a-col flex="auto">
        <a-menu
          v-model:selectedKeys="current"
          mode="horizontal"
          @click="onMenuClick"
          :items="items"
        />
      </a-col>
      <div class="user-login-status">
        <div v-if="loginUserStore.loginUser.id">
          <a-dropdown>
            <a-space>
              <a-avatar :src="loginUserStore.loginUser.userAvatar"/>
              {{ loginUserStore.loginUser.userName ?? '无名' }}
            </a-space>
            <template #overlay>
              <a-menu>
                <a-menu-item @click="doLogout">
                  <LogoutOutlined/>
                  退出登录
                </a-menu-item>
              </a-menu>
            </template>
          </a-dropdown>
        </div>
        <div v-else>
          <a-button type="primary" href="/user/login">登录</a-button>
        </div>
      </div>
    </a-row>
  </div>
</template>
<script lang="ts" setup>
import {h, ref} from 'vue'
import {HomeOutlined} from '@ant-design/icons-vue'
import type {MenuProps} from 'ant-design-vue'
import {useRouter} from 'vue-router'
import {useLoginUserStore} from "@/stores/useLoginUserStore";
import {userLogoutUsingPost} from "@/api/userController";
import {message} from "ant-design-vue";

const loginUserStore = useLoginUserStore()
const items = ref<MenuProps['items']>([
  {
    key: '/',
    icon: () => h(HomeOutlined),
    label: '主页',
    title: '主页',
  },
  {
    key: '/admin/userManage',
    label: '管理页面',
    title: '管理页面',
  },
  {
    key: 'others',
    label: h('a', {href: 'https://www.codefather.cn', target: '_blank'}, '编程导航'),
    title: '编程导航',
  },
])

const router = useRouter()
const current = ref<string[]>([''])

// 路由跳转事件
const onMenuClick = ({key}) => {
  router.push({
    path: key,
  })
}

//钩子函数，每次跳转到新的页面都会执行，当前要高亮的菜单项
router.afterEach((to) => {
  current.value = [to.path]
})

// 用户注销
const doLogout = async () => {
  const res = await userLogoutUsingPost()
  console.log(res)
  if (res.data.code === 0) {
    loginUserStore.setLoginUser({
      userName: '未登录',
    })
    message.success('退出登录成功')
    await router.push('/user/login')
  } else {
    message.error('退出登录失败，' + res.data.message)
  }
}


</script>

<style scoped>
.title-bar {
  display: flex;
  align-items: center;
}

.title {
  color: black;
  font-size: 18px;
  margin-left: 16px;
}

.logo {
  height: 48px;
}
</style>

