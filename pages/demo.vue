<template>
    <div>
    <el-input v-model="choose" placeholder="请输入基金代号"></el-input>
    <el-input v-model="money" placeholder="请输入买入金额"></el-input>
    <el-button type="primary" v-on:click="fetchGszzl">添加</el-button>
    <el-table
      :data="tableData"
      style="width: 100%">
      <el-table-column
        prop="gztime"
        label="日期"
        width="180">
      </el-table-column>
      <el-table-column
        prop="name"
        label="基金名称"
        width="180">
      </el-table-column>
      <el-table-column
        prop="fundcode"
        label="基金code"
        width="180">
      </el-table-column>
      <el-table-column
        prop="gszzl"
        sortable
        label="涨幅">
      </el-table-column>
      <el-table-column
        prop="money"
        label="金额">
      </el-table-column>
      <el-table-column
        prop="more"
        sortable
        label="盈利">
      </el-table-column>
    </el-table>
    <div>今天总盈利{{value}}</div>
    </div>
  </template>

<script>
    import axios from 'axios';
    import Vue from 'vue';
    import { Table, TableColumn, Input, Button } from 'element-ui';
    import 'element-ui/lib/theme-chalk/index.css';
    Vue.use(Table)
    Vue.use(TableColumn)
    Vue.use(Input)
    Vue.use(Button)
    export default {
        name: 'demo',

        // data() {
        //     return {
        //         value: 0
        //     };
        // },

        data() {
        return {
          tableData: [],
          choose: ['005312', '000248', '007300', '050021','161725', '501057'],
          money:[11692, 3244,10658, 11681, 12324, 11637],
          value: 0
        }
      },

      async mounted() {
        const {choose, money} = this.$data;
          const res = await this.fetchList(choose);
          let value = 0;
          res.map((i, index)=> value+=i*money[index]/100);
          this.$data.value = value.toFixed(2)
      },

        methods: {
            parse(str) {
                return JSON.parse(str.slice(8, -2));
            },

            async fetchGszzl(code) {
              const {choose, money} = this.$data;
              const {data} = await axios(`api/js/${code}.js?rt=${new Date().getTime()}`);
              const newData = this.parse(data);
              console.log(choose)
              const index = choose.indexOf(code)
              newData.money = money[index];
              newData.more = (money[index] * newData.gszzl / 100).toFixed(2);
              this.$data.tableData.push(newData);

              return this.parse(data).gszzl;
          },

            async fetchList(arr) {
                const list = arr.map(v => {
                    return this.fetchGszzl(v);
                });
                return Promise.all(list);
            }
        }
    }
</script>

<style>

</style>
<!-- async fetchGszzl() {
    const code = this.$data.choose;
    const {money, choose} = this.$data;
    const {data} = await axios(`api/js/${code}.js?rt=${new Date().getTime()}`);
    const newData = this.parse(data);
    newData.money = money;
    newData.more = money * newData.gszzl / 100;
    this.$data.tableData.push(newData);
    console.log(this.$data.tableData);
    this.$data.choose = '';
    this.$data.money = '';
}, -->