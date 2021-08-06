---
title: 学习vue遇到的坑
date: 2020-04-23 16:25:00
categories: Vue
tags: ['技术']
comment: false
---

## 入门Vue，第一篇
<!-- more -->
### 安装环境就不说了，直接创建项目
### 打开GitBash
### 全局安装vue-cli
```bash
cnpm install vue-cli -g
```

### 创建项目
```bash
vue init webpack "项目名称"
```

### 进入项目文件夹
```bash
cd "项目名称"
```
### 安装相关依赖
```bash
cnpm install
```
### 运行初步demo
```bash
cnpm run dev
```

### 用vscode终端输入来安装axios
```bash
cnpm install axios
```

### 在main.js配置全局axios
```javascript
import axios from 'axios'

Vue.prototype.$axios = axios

axios.defaults.timeout = 5000;

axios.defaults.baseURL = 'http://接口地址:端口号/';
```
### 在config文件夹中的index.js中找到proxyTable
### 改成这样
```javascript
proxyTable: {
  '/api':{
    target:"http://接口地址:端口号/",//接口地址
    source:false,// 如果是https接口，需要配置这个参数
    changeOrigin:true,//允许跨域
    ws: true, //打开websocket
    pathRewrite:{
      '^/api':'/'//把target用api来代替
    }
  }
}
```
### 然后开始调接口，新建一个index.vue
### 在router文件夹里面的index.js配置如下
```javascript
import Vue from 'vue'
import Router from 'vue-router'
import HelloWorld from '@/components/HelloWorld'
import Index from '@/components/Index'//注册组件

Vue.use(Router)

export default new Router({
  routes: [
    {
      path: '/',
      name: 'HelloWorld',
      component: HelloWorld
    },
    {
      path: '/Index',
      name: '首页',
      component: Index
    }
  ]
})
```

### 新页面结构如下
```javascript
<template>
  <div class="hello">
    账号：<input type="text" v-model="username"><br>
    密码：<input type="password" v-model="password"><br>
    <button @click="Login">登录</button>
  </div>
</template>

<script>
import axios from 'axios'
import Qs from 'qs'
export default {
  name: 'HelloWorld',
  data () {
    return {
      username:'',//绑定v-model
      password:''
    }
  },
  methods:{
      //post方法
      Login(){
        this.$axios.post('/api/Admin/Login',
        //Qs.stringify将对象序列化
        Qs.stringify({
          UserName:this.username,//参数取input框内的值
          Password:this.password
        }),{headers:{'Content-Type':'application/x-www-form-urlencoded'}}).then((res)=>{
        //需要注意，post方法需要加上这个headers头
        if(res.data.code!=0){
          alert(res.data.res.msg);//接口错误弹出接口返回信息
        }
        else{
          var token=res.data.res.data.adminToken;
          console.log(token);//输出用户token
        }
      })
    }
  }
}
</script>
```
### 没什么难点，大部分时间花在了解决跨域的问题
