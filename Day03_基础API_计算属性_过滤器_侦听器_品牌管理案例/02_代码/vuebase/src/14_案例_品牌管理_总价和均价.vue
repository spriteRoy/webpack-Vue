<template>
  <div id="app">
    <div class="container">
      <!-- 顶部框模块 -->
      <div class="form-group">
        <div class="input-group">
          <h4>品牌管理</h4>
        </div>
      </div>

      <!-- 数据表格 -->
      <table class="table table-bordered table-hover mt-2">
        <thead>
          <tr>
            <th>编号</th>
            <th>资产名称</th>
            <th>价格</th>
            <th>创建时间</th>
            <th>操作</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="obj in list" :key="obj.id">
            <td>{{ obj.id }}</td>
            <td>{{ obj.name }}</td>

            <!-- 如果价格超过100，就有red这个类 -->
            <td :class="{red: obj.price > 100}">{{ obj.price }}</td>

            <td>{{ obj.time | formatDate }}</td>
            <td><a href="#" @click="delFn(obj.id)">删除</a></td>
          </tr>
          <tr style="background-color: #eee">
            <td>统计:</td>
            <td colspan="2">总价钱为: {{ allPrice }}</td>
            <td colspan="2">平均价: {{ avgPrice }}</td>
          </tr>
        </tbody>
        <tfoot v-show="list.length === 0">
          <tr>
            <td colspan="5" style="text-align: center">暂无数据</td>
          </tr>
        </tfoot>
      </table>

      <!-- 添加资产 -->
      <form class="form-inline">
        <div class="form-group">
          <div class="input-group">
            <input
              type="text"
              class="form-control"
              placeholder="资产名称"
              v-model="name"
            />
          </div>
        </div>
        &nbsp;&nbsp;&nbsp;&nbsp;
        <div class="form-group">
          <div class="input-group">
            <input
              type="text"
              class="form-control"
              placeholder="价格"
              v-model.number="price"
            />
          </div>
        </div>
        &nbsp;&nbsp;&nbsp;&nbsp;

        <button class="btn btn-primary" @click.prevent="addFn">添加资产</button>
      </form>
    </div>
  </div>
</template>

<script>
// 目标: 总价和均价显示
// 1. 末尾补tr - 显示总价和均价
import moment from 'moment'
export default {
  data() {
    return {
      name: "", // 名称
      price: 0, // 价格
      list: [
        { id: 100, name: "外套", price: 199, time: new Date('2010-08-12')},
        { id: 101, name: "裤子", price: 34, time: new Date('2013-09-01') },
        { id: 102, name: "鞋", price: 25.4, time: new Date('2018-11-22') },
        { id: 103, name: "头发", price: 19900, time: new Date('2020-12-12') }
      ],
    };
  },
  methods: {
    addFn() {
      if (this.name.trim().length === 0 || this.price === 0) {
        alert("不能为空")
        return
      }

      // 解决bug: 无数组新增-list没有数据, id需要给一个固定值(以后id都是后台生成, 现在是模拟给一个id)
      let id = this.list.length > 0 ? this.list[this.list.length - 1].id + 1 : 100

      this.list.push({
        // 当前数组最后一个对象的id+1作为新对象id值
        id: id,
        name: this.name,
        price: this.price,
        time: new Date()
      })
    },
    delFn(id){
     // 通过id找到这条数据在数组中下标
      let index = this.list.findIndex(obj => obj.id === id)
      this.list.splice(index, 1)
    }
  },
  filters: {
    formatDate(val) {
      return moment(val).format("YYYY-MM-DD");
    },
  },
  // 2. 计算属性
  computed: {
      allPrice(){
          // 3. 求总价
          return this.list.reduce((sum, obj) => sum += obj.price, 0)
      },
      avgPrice(){
          // 4. 求均价 - 保留2位小数
          return (this.allPrice / this.list.length).toFixed(2)
      }
  }
};
</script>

<style >
.red {
  color: red;
}
</style>