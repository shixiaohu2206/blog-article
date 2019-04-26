---
title: vue框架jsonp跨域获取不同源数据
date: 2017-12-25
tags:
  - VUE
  - JSONP
categories:
  - 代码
---

## 遇到解决不同源跨域问题。记录如下

> NodeJS 版本为 v8.9.0
> 前端 vue 使用 vue-cli 脚手架搭建，版本为 2.9.2
> 后台用的 Express 框架，版本为

## 后台部分

- 先是后台 Express 框架部分。 在 routes 文件夹下新建一个路由文件，文件名为 topGoods.js

```javascript
var express = require("express");
var router = express.Router();
var curl = require("../utils/curl");
var request = require("request"); // 引入request模块，进行模拟请求

// 使用了大淘客的api
const topGoodsApi =
  "http://api.dataoke.com/index.php?r=Port/index&type=top100&appkey=59f42fbc5a&v=2";

//get方式模拟请求接口数据
router.get("/", function(req, res, next) {
  let page = req.query.page;
  request(topGoodsApi, function(error, response, body) {
    res.jsonp(JSON.parse(body));
  });
});
module.exports = router;
```

> 启动 Express 服务后，页面访问 http://localhost:3000/topgoods，即可可看数据以JSON格式展示

## 前台部分

- 下面是前端部分。在 VUE 框架中使用 AJAX，需要用到插件 vue-resource。

```
//安装vue-resource
$ npm install vue-resource -g
```

- 然后在 vue 中找到 src 目录下 main.js（入口 JS 文件），引入 vue-resource
  > 原本想实现一个简单的分页，查看大淘客的接口，才发现接口不支持分页。所有只在页面实现分页传参

```javascript
<template>
    <div>
        <div>
            <a href="javascript:void(0)" @click="setPage(parseInt(currentPage) - 1)">上一页</a>
            <a href="javascript:void(0)" @click="setPage(parseInt(currentPage) + 1)">下一页</a>
        </div>
        <ul>
            <li v-for="item in result">
                <a :href="item.Quan_link"><img :src="item.Pic" :alt="item.Introduce"></a>
                <a :href="item.Quan_link">{{ item.D_title }}</a>
            </li>
        </ul>
    </div>
</template>
<script>
  export default {
    name: "test",
    methods: {
      setPage: function (page) {
        if (page <= 1) {
          this.currentPage = 1
        } else if (page >= this.totalNum) {
          this.currentPage = this.totalNum
        } else {
          this.currentPage = page
        }
        window.location.href = this.$route.path + "?page=" + this.currentPage
      }
    },
    data () {
      return {
        result: [],
        currentPage: 1, // 当前页数
        totalNum: 20 // 总页数
      }
    },
    created () { // vue钩子函数，在成功创建vue对象时执行
      this.currentPage = this.$route.query.page || 1
      this.$http.jsonp("http://localhost:3000/topgoods?page=" + this.currentPage, { credentials: true }).then(response => {
        if (response.status === 200) {
          this.result = response.body.result
        }
      }, response => {
        console.log(response)
      })
    }
  }
</script>
<style scoped>
ul li {
    text-decoration: none;
    list-style-type:none;
    width: 160px;
    height: 230px;
    float: left;
    margin-left: 10px;
}
img {
    width: 150px;
    height: 170px;
}
</style>
```

- 新建一个 test 路由，找到 router 文件下 index.js

```javascript
import Vue from "vue";
import Router from "vue-router";
import HelloWorld from "@/components/HelloWorld";
import Header from "@/components/Header";
import Test from "@/components/Test"; // 引入test组件

Vue.use(Router);

export default new Router({
  mode: "history", // 开启history模式，去除url中的"#"
  routes: [
    {
      path: "/",
      name: "HelloWorld",
      component: HelloWorld
    },
    {
      path: "/header",
      name: "Header",
      component: Header
    },
    {
      path: "/test", // 新增一个test路由
      name: "Test",
      component: Test
    }
  ]
});
```

> 浏览器访问http://localhost:8080/test，即可看到数据展示

- 注意点：vue 的 url 是默认 hash 模式，导致 url 中带有#，不美观。解决方法为 router/index.js 文件下，开启"history"模式。
