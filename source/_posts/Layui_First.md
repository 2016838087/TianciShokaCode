---
title: Layui小知识
date: 2021-08-19 15:00:00
categories: Other
tags: ['技术']
comment: false
---
### 遇到的一些问题及解决方法
<!-- more -->
### 首先定义Layui需要的组件
``` javascript
var $ = layui.$;
var admin = layui.admin;
var table = layui.table;
var layer = layui.layer;
var form = layui.form;
```
### 下拉框从接口获取数据并赋值
```html
<div class="layui-input-inline" style="width:100px;">
  <select asp-for="绑定字段" name="绑定字段" class="绑定字段Mode" autocomplete="off" lay-search>
  </select>
</div>
```
```javascript
$(function () {
  $.ajax({
      url: '@Url.Action("Create", "User")',//请求地址（MVC写法，方法，控制器名）
      type: 'POST',
      dataType: 'json',
      contentType: "application/json; charset=utf-8",
      success: function (result) {
        if (result.code == 200) {
            for (var k in result.data) {
                $(".绑定字段Mode").append("<option value='" + result.data[k].KeyName + "'>" 
                + result.data[k].Value + "</option>");//通过class添加选项
            }
            layui.use('form', function () {
                var form = layui.form;
                form.render();
            });
        }
        else
        {
            layer.msg(result.msg, { icon: 2, offset: '200px', time: 3000 })
        }
      }
  });
});
```
### 获取当前页面表单里面的数据
```javascript
var data = form.val('formId');//通过表单Id取全部字段数据
```
### 获取子页面表单数据
### 逻辑：打开添加表单（新页面Create）点提交调用POST方法传值（dataValue）
```javascript
layer.open({
  type: 2,
  title: '添加用户',
  id: 'CreateUser',
  content: '@Url.Action("Create", "User")',//打开页面（MVC写法，对应页面及GET方法，控制器名）
  area: ['528px', '468px'],
  maxmin: true,
  btn: ['提交', '关闭'],
  yes: function (index, layero) {
    var iframeWindow = layero.find('iframe')[0].contentWindow;
    var dataValue = iframeWindow.layui.form.val("子页面form表单Id");//表单数据
    $.ajax({
      url: '@Url.Action("Create", "User")',//请求POST地址（MVC写法，POST方法，控制器名）
      type: 'POST',
      dataType: 'json',
      data: JSON.stringify(dataValue),
      contentType: "application/json; charset=utf-8",
      success: function (result) {
        if (result.responseCode == 200) {
          layer.msg(result.Message, { offset: '200px', time: 3000 })
          layer.close(index);
        } else {
          layer.msg(result.Message, { icon: 2, offset: '200px', time: 3000 })
        }
      }
    });
  },
  cancel: function () {
      return true;
  },
  end: function () {
    table.reload('当前页面列表TableId', {
        page: {
            curr: 1
        }
    });
  }
});
```
### 给新页面表单赋默认值，点击修改的场景
### 在控制器内Edit对应的GET方法返回值就可以了，Edit页面form表单绑定