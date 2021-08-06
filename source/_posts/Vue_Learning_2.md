---
title: vue+elementui遇到的坑
date: 2020-04-24 21:30:00
categories: Vue
tags: ['技术']
comment: false
---
## 入门vue，第二篇
<!-- more -->
### 今天做一个vue+elementui的分页列表
### 首先安装elementui
```bash
npm i element-ui -S
```

### 然后在main.js里面配置
```javascript
//elementui配置
import ElementUI from 'element-ui'
import 'element-ui/lib/theme-chalk/index.css'
Vue.use(ElementUI)
```
### 注意事项，main.js这里要改个东西
```javascript
new Vue({
  el: '#app',
  router,
  //components: { App },//vue1.0写法
  template: '<App/>',
  render: h => h(App)//vue2.0写法
  //render函数是渲染一个视图，然后提供给el挂载，如果没有render那页面什么都不会出来
})
```
### render: h => h(App)这是新加的，上面的注释掉，不然会报错
### 贴上列表的全部代码
```javascript
<template>
<el-container style="height: 500px; border: 1px solid #eee">
  <el-aside width="200px" style="background-color: rgb(238, 241, 246)">
    <el-menu :default-openeds="['1','3']">
      <el-submenu index="1">
        <template slot="title"><i class="el-icon-message"></i>导航1</template>
          <el-menu-item index="1-1">选项1</el-menu-item>
          <el-menu-item index="1-2">选项2</el-menu-item>
          <el-menu-item index="1-3">选项3</el-menu-item>
      </el-submenu>
      <el-submenu index="2">
        <template slot="title"><i class="el-icon-message"></i>导航2</template>
          <el-menu-item index="1-1">选项1</el-menu-item>
          <el-menu-item index="1-2">选项2</el-menu-item>
          <el-menu-item index="1-3">选项3</el-menu-item>
      </el-submenu>
      <el-submenu index="3">
        <template slot="title"><i class="el-icon-message"></i>导航3</template>
          <el-menu-item index="1-1">选项1</el-menu-item>
          <el-menu-item index="1-2">选项2</el-menu-item>
          <el-menu-item index="1-3">选项3</el-menu-item>
          <el-menu-item index="1-4">选项4</el-menu-item>
      </el-submenu>
    </el-menu>
  </el-aside>
  
  <el-container>
    <el-header style="text-align: right; font-size: 12px">
      <el-dropdown>
        <i class="el-icon-setting" style="margin-right: 15px"></i>
        <el-dropdown-menu slot="dropdown">
          <el-dropdown-item>查看</el-dropdown-item>
          <el-dropdown-item>新增</el-dropdown-item>
          <el-dropdown-item>删除</el-dropdown-item>
        </el-dropdown-menu>
      </el-dropdown>
      <span>王小虎</span>
    </el-header>
    
    <el-main>
      <el-table :data="list" >
      <!-- <el-table :data="list.slice((pageNow-1)*pageSize,pageNow*pageSize)" > -->
          <!-- 对数据请求本地的处理，如果调用接口直接赋值list -->
        <el-table-column prop="id" label="id" width="40">
        </el-table-column>
        <el-table-column prop="imageUrl" label="图片路径" width="420">
        </el-table-column>
        <el-table-column prop="detail" label="详情" width="140">
        </el-table-column>
        <el-table-column prop="isActive" label="是否可用">
        </el-table-column>
      </el-table>
    </el-main>
    <el-pagination
        background
        layout="total, sizes, prev, pager, next, jumper"
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
        :total="totalCount"
        :current-page="pageNow"
        :page-sizes="[5, 10, 15, 20, 25]"
        :page-size="pageSize"
        >
        <!-- 背景色，组件布局，pageSize事件，pageNow事件，总条目数，当前页，当前页数，每页个数 -->
</el-pagination>
  </el-container>
</el-container>
</template>
<style>
/* 总数，每页条数，上页，，当前页，下页，跳转 */
/* total, sizes, prev, pager, next, jumper */
  .el-header {
    background-color: #B3C0D1;
    color: #333;
    line-height: 60px;
  }
  
  .el-aside {
    color: #333;
  }
</style>

<script>
import axios from 'axios'
export default {
    data() {
    return {
      list:[],
      pageNow:1,//绑定控件当前页
      pageSize:10,//绑定控件当前页数量
      totalCount:1000//绑定控件总数
    }
  },
  created(){
      this.GetList();
      //created:在模板渲染成html前调用，即通常初始化某些属性值，然后再渲染成视图
      //mounted:在模板渲染成html后调用，通常是初始化页面完成后，再对html的dom节点进行一些需要的操作
  },
  methods:{
    handleSizeChange: function (pageSize) {
            this.pageSize = pageSize;//每页下拉显示数据
            this.GetList(this.pageSize);//调用方法传参
    },
    handleCurrentChange: function(pageNow){
            this.pageNow = pageNow;//点击第几页
            this.GetList(this.pageNow);//调用方法传参
    },
    GetList(){
      this.$axios.get('/api/Tianci/Images',
        {
          params:{
            PageNow:this.pageNow,
            PageSize:this.pageSize
          }
        }).then((res)=>{
        if(res.data.code==0){
            //列表赋值给list
            this.list=res.data.res.data.rows;
            //总页数返回给绑定的参数
            this.totalCount=res.data.res.data.total;

        }
        else{
            alert("数据为空");
        }
      });
    }
  }
}
</script>
```
### 主要是看el-main标签到最后script代码，就不一一解释了，主要是渲染
### 然后npm run build打包，这里会遇到三个问题
### 线上预览，是没问题的
![本地预览正常](1.jpg)
### 第一个问题，页面全白，打开config文件夹下面的index.js找到这里
```javascript
// Paths
assetsRoot: path.resolve(__dirname, '../dist'),
assetsSubDirectory: 'static',
assetsPublicPath: './',//打包后页面空白，/改成./
```
### 第二个问题，引用的文件报错，找到build文件夹下面的utils.js

```javascript
return ExtractTextPlugin.extract({
    use: loaders,
    fallback: 'vue-style-loader',
    publicPath: '../../' //文件路径异常，添加这句
    })
```
### 最后打包遇到第三个问题
![打包之后跑版](2.jpg)
### 跟本地预览样式有一些差异，但是也没有报错
### 下期再发解决的吧，现在也一直没找到原因