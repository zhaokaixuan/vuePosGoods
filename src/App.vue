<template>
  <div id="app">
    <!--左侧导航-->
    <leftNav></leftNav>
    <!--操作区域-->
    <div class="main">
      <router-view></router-view>
    </div>
  </div>
</template>
 
<script>
import leftNav from '@/components/common/leftNav'
export default {
  name: 'app',
  components: {
    leftNav
  }
}
</script>
 
<style>
#app {
  font-family: 'Microsoft YaHei', 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: left;
  color: #2c3e50;
  height: 100%;
}

.main {
  margin-left:80px;
  background-color: #EFF2F7;
  height: 100%;
  overflow: auto;
}

.el-tabs__content {
  padding: 0 10px;
}
.el-table .cell, .el-table th>div {
    padding-left: 10px;
    padding-right: 10px;
}
</style>