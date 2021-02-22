<template>
  <el-container class="home-container">
    <el-header>
      <div>
        <img src="../assets/img/onlylogo.png" alt="" />
        <span>Free电商管理系统</span>
      </div>
      <el-button type="info" @click="logout">退出</el-button>
    </el-header>
    <el-container>
      <el-aside :width="isCollapse ? '64px' : '200px'">
        <!-- 折叠菜单栏 -->
        <div class="toggle-button" @click="toggleCollapse">
          |||
        </div>
        <el-menu
          background-color="#333744"
          text-color="#fff"
          active-text-color="#409bff"
          unique-opened
          :collapse="isCollapse"
          :collapse-transition="false"
          router
          :default-active="activePath"
        >
          <!-- default-active 值是子文件的id， -->
          <el-submenu
            :index="item.id + ''"
            :key="item.id"
            v-for="item in menulist"
          >
            <template slot="title">
              <i :class="iconsObj[item.id]"></i>
              <span>{{ item.authName }}</span>
            </template>
            <!-- 二级菜单 -->
            <el-menu-item
              :index="'/' + subItem.path"
              :key="subItem.id"
              v-for="subItem in item.children"
              @click="saveNavState('/' + subItem.path)"
            >
              <template slot="title">
                <i class="el-icon-menu"></i>
                <span>
                  {{ subItem.authName }}
                </span>
              </template>
            </el-menu-item>
          </el-submenu>
        </el-menu>
      </el-aside>
      <el-main>
        <!-- 路由占位符 -->
        <router-view></router-view>
      </el-main>
    </el-container>
  </el-container>
</template>

<script>
export default {
  data() {
    return {
      menulist: [],
      iconsObj: {
        '125': 'el-icon-s-check',
        '103': 'el-icon-s-tools',
        '101': 'el-icon-goods',
        '102': 'el-icon-phone',
        '145': 'el-icon-data-analysis'
      },
      // 是否折叠
      isCollapse: false,
      // 被激活的链接地址
      activePath: ''
    }
  },
  created() {
    this.getMenuList()
    // 将存储在sessionStorage中的地址存放宅activePath中
    this.activePath = window.sessionStorage.getItem('activePath')
  },
  methods: {
    logout() {
      window.sessionStorage.clear()
      this.$router.push('/login')
    },
    async getMenuList() {
      const { data: res } = await this.$http.get('menus')
      if (res.meta.status !== 200) return this.$message.error(res.meta.msg)
      this.menulist = res.data
      // console.log(res)
    },
    toggleCollapse() {
      this.isCollapse = !this.isCollapse
    },
    // 单击二级子项时将当前左侧链接的激活状态传入session
    saveNavState(activePath) {
      window.sessionStorage.setItem('activePath', activePath)
      this.activePath = activePath
    }
  }
}
</script>

<style lang="less" scoped>
.el-header {
  background-color: #373d41;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding-left: 0;
  user-select: none;
  // box-sizing: border-box;
  > div {
    color: azure;
    line-height: 60px;
    font-size: 20px;
    display: flex;
    img {
      height: 60px;
      width: 60px;
    }
    > span {
      margin-left: 15px;
    }
  }
}

.home-container {
  height: 100%;
  .el-aside {
    // height: 100vh;
    background-color: #333744;
    .el-menu {
      color: #eaedf1;
      border-right: none;

      i {
        margin-right: 10px;
      }
    }
  }
  .el-main {
    background-color: #eaedf1;
  }
}
.toggle-button {
  background-color: #4a5064;
  font-size: 10px;
  line-height: 24px;
  color: white;
  text-align: center;
  letter-spacing: 0.2rem;
  cursor: pointer;
  user-select: none;
}
</style>
