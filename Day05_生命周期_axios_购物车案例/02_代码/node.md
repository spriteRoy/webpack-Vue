```javascript
data() {
    return {
      bName: "",
      bookObj: { // 参数名提前和后台的参数名对上-发送请求就不用再次对接了
          bookname: "",
          author: "",
          publisher: ""
      }
    };
  }
```

```javascript
methods:{
       sendFn(){
       axios({
           url: "/api/addbook",
           method: "POST",
           data: {
               appkey: "7250d3eb-18e1-41bc-8bb2-11483665535a",
               // 对象解构
               ...this.bookObj
               //等同于下面
            //    bookname: this.bookObj.bookname,
            //    author: this.bookObj.author,
            //    publisher: this.bookObj.publisher
           }
       }) 
    }
}
```







```html
<input ref="myInp" type="text" placeholder="这是一个输入框" v-if="isShow">
<button v-else @click="btn">点击我进行搜索</button>
```

```javascript
// 目标: 点按钮(消失) - 输入框出现并聚焦
// 1. 获取到输入框
// 2. 输入框调用事件方法focus()达到聚焦行为
export default {
    data(){
        return {
            isShow: false
        }
    },
    methods: {
        async btn(){
            this.isShow = true;
            // this.$refs.myInp.focus()
            // 原因: data变化更新DOM是异步的
            // 输入框还没有挂载到真实DOM上
            // 解决:
            // this.$nextTick(() => {
            //     this.$refs.myInp.focus()
            // })
            // 扩展: await取代回调函数
            // $nextTick()原地返回Promise对象
            await this.$nextTick()
            this.$refs.myInp.focus()
        }
    }
}
```

```javascript
// Vue.directive()方法必须写在new Vue之前
Vue.directive("gfocus",{
    inserted(el){
        el.focus()
    }
})
new Vue({
    render: h => h(App),
}).$mount('#app')
```

```javascript
// inserted方法：指令所在标签被插入到网页上时触发
export default {
    directives: {
        focus:{
            inserted(el){
                el.focus()
                console.log(el);
            }
        }
    }
}
```

```html
<div class="footer_wrap">
    <span @click="btn('/find')">发现音乐</span>
    <span @click="btn('/my')">我的音乐</span>
    <span @click="btn('/part')">朋友</span>
</div>
```

```javascript
methods: {
    btn(targetPath){
        this.$router.push({
            path: targetPath
        })
    }
}
```

```html
<div class="footer_wrap">
    <span @click="btn('Find')">发现音乐</span>
    <span @click="btn('My')">我的音乐</span>
    <span @click="btn('Part')">朋友</span>
</div>
```

```javascript
methods: {
    btn(targetPath,targetName){
        this.$router.push({
            name:targetName
        })
    }
}
```

```javascript
// 名字最好叫routes
const routes = [
    { path: '/find',name:'Find', component: Find },
    { path: '/my',name:'My', component: My },
    { path: '/part',name:'Part', component: Part },
]
```

> 目标: 编程式导航 - 跳转路由传参
>
> 方式1:params => $route.params.参数名
>
> 方式2:query => $route.query.参数名
>
> 重要: path会自动忽略params
>
> 推荐: name+query方式传参
>
> 注意: 如果当前url上"hash值和?参数"与你要跳转到的"hash值和?参数"一致, 爆出冗余导航的问题, 不会跳转路由

> 目标: 声明式导航 - 激活类名区别
>
> 1. url上hash值(#/home/recommend) 包含 导航的href值(#/home) - 当前a就有 "router-link-active"  (模糊)
> 2. url上hash值(#/home/recommend) 等于 导航的href值(#/home/recommend) - 当前a就有"router-link-exact-active" (精确)

> 目标: 路由守卫
>
> 场景: 当你要对路由权限判断时
>
> // 不知道参数对象是什么意思就打印
>
> 语法: router.beforeEach((to, from, next)=>{//路由跳转"之前"先执行这里, 决定是否跳转})
>
> 参数1: 要跳转到的路由 (路由对象信息)    目标
>
> 参数2: 从哪里跳转的路由 (路由对象信息)  来源
>
> 参数3: 函数体 - next()才会让路由正常的跳转切换, next(false)在原地停留, next("强制修改到另一个路由路径上")
>
> 注意: 如果不调用next, 页面留在原地

```javascript
// 安装vant的2.12.15版本
import Button from "vant/lib/button";
import "vant/lib/button/style";
export default {
    components: {
        // [Button.name]: Button,
        // 等价的
        VanButton: Button
    },
```

```html
<template>
    <div>
        <van-button type="primary">主要按钮</van-button>
        <van-button type="success">成功按钮</van-button>
        <van-button type="default">默认按钮</van-button>
        <van-button type="warning">警告按钮</van-button>
        <van-button type="danger">危险按钮</van-button>
    </div>
</template>
```

```html
<van-button type="primary" @click="btn">主要按钮</van-button>
```

```javascript
// 目标：使用弹窗
// 1.找到vant文档
// 2.引入
// 3.在恰当时机，调用此函数（还可以用组件的方式）
import { Dialog } from 'vant';
export default {
    methods: {
        btn(){
            Dialog({ message: '提示' });
        }
    }
};
```

```html
<van-tabbar v-model="active" route>
    <van-tabbar-item replace to="/layout/home" icon="home-o">首页</van-tabbar-item>
    <van-tabbar-item replace to="/layout/search" icon="search">搜索</van-tabbar-item>
</van-tabbar>
```

```javascript
// 测试封装的api方法
import {recommendMusicAPI} from '@/api'
async function fn(){
    // api方法原地会得到axios请求在原地的Promise对象(里面有一个ajax请求)
    const res = await recommendMusicAPI() 
    console.log(recommendMusicAPI);
}
fn()
```

> 目标: 封装网络请求
>
> 1. src/utils/request.js -  设置基地址, 导出axios函数
> 2. src/api/Home.js - 请求方法axios({url: "具体"})
> 3. src/api/index.js - 把Home.js里函数引入过来统一导出
> 4. 具体逻辑页面, 引入api/index.js请求方法, async+await等待axios的结果回来

```javascript
commit(mutation名称, 载荷)
    //       // 调用mutation方法，提交mutation名称
    //       // 参数一：mutation方法名称
    //       // 参数二：要传递的载荷payload，就是要传入的参数async inputFn() {
    // 目标: 输入框改变-逻辑代码-慢点执行
    // 解决: 防抖
    // 概念: 计时n秒, 最后执行一次, 如果再次触发, 重新计时
    // 效果: 用户在n秒内不触发这个事件了, 才会开始执行逻辑代码
    if (this.timer) clearTimeout(this.timer)
    this.timer = setTimeout(async () => {
        this.page = 1; // 点击重新获取第一页数据
        this.finished = false // 输入框关键字改变-可能有新数据(不一定加载完成了)
        // 输入框值改变
        if (this.value.length === 0) {
            // 搜索关键词如果没有, 就把搜索结果清空阻止网络请求发送(提前return)
            this.resultList = [];
            return;
        }
        const res = await this.getListFn();
        console.log(res);
        // 如果搜索结果响应数据没有songs字段-无数据
        if (res.data.result.songs === undefined) {
            this.resultList = [];
            return
        }
        this.resultList = res.data.result.songs;
        this.loading = false;
    }, 900)
}
```

> commit(mutation名称, 载荷)
>
> 调用mutation方法，提交mutation名称
>
> 参数一：mutation方法名称
>
> 参数二：要传递的载荷payload，就是要传入的参数

```javascript
import { mapMutations } from 'vuex'
export default {
    methods: {
        // 子模块中的方法可以直接通过全局的方式调用
        updateToken2(){
            this.$store.commit('updateToken')
        },
        ...mapMutations(['updateToken'])
    }
}
```





```javascript
modules: {
    user:{
      namespaced:true,
      state: {
        token:'123456789'
      },
      mutations: {
        updateToken(state){
          state.token = '子模块中的token'
        }
      }
    }
  }
```

```javascript
// 全局的mapMutations
import { mapMutations } from 'vuex'

export default {
    methods: {
        // 子模块中的方法可以直接通过全局的方式调用
        updateToken2(){
            // 方式1：采用路径方式调用子模块中的mutation方法
            this.$store.commit('user/updateToken')
        },
        // 方式2：使用辅助函数的方式调用子模块中的mutation方法
        ...mapMutations(['user/updateToken']),
        updateToken(){
            this['user/updateToken']()
        }
    }
}
```

```javascript
import { createNamespacedHelpers } from "vuex";
const {mapMutations} = createNamespacedHelpers('user')
export default{
    methods: {
        ...mapMutations(['updateToken']),
    }
}
```

```html
<template>
  <div>
      <input ref="myInp" type="text" placeholder="这是一个输入框" v-if="isShow">
      <button v-else @click="btn">点击我进行搜索</button>
  </div>
</template>
```

```javascript
// 目标: 点按钮(消失) - 输入框出现并聚焦
// 1. 获取到输入框
// 2. 输入框调用事件方法focus()达到聚焦行为
export default {
    data(){
        return {
            isShow: false
        }
    },
    methods: {
        async btn(){
            this.isShow = true;
            // this.$refs.myInp.focus()
            // 原因: data变化更新DOM是异步的
            // 输入框还没有挂载到真实DOM上
            // 解决:
            // this.$nextTick(() => {
            //     this.$refs.myInp.focus()
            // })

            // 扩展: await取代回调函数
            // $nextTick()原地返回Promise对象
            // Promise对象里面包裹的是更新DOM的异步任务
            await this.$nextTick()
            this.$refs.myInp.focus()
        }
    }
}
```

