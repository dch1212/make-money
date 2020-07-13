<template>
    <div v-html="value">

    </div>

</template>

<script>
  export default {
      name: 'demo',

      data() {
          return {
              value: 0
          };
      },

    //   data() {
    //   return {
    //     tableData: [],
    //     choose:'',
    //     money:'',
    //     chooseArr: [],
    //     moneyArr: []
    //   }
    // },

      async mounted() {
          const choose = ['005312', '000248', '007300', '050021','161725', '501057'];
          const money = [11692, 3244,10658, 11681, 12324, 11637];
          const res = await this.fetchList(choose);
          console.log(res);
          let value = 0;
          res.map((i, index)=> value+=i*money[index]/100);
          console.log(value)
          this.$data.value = value
      },

      methods: {

          // add(){
          //     console.log(this.$data);
          //     const {choose, money} = this.$data;
          //     this.$data.chooseArr.push(choose);
          //     this.$data.moneyArr.push(money);
          // },

          parse(str) {
              return JSON.parse(str.slice(8, -2));
          },

          async fetchGszzl(code) {
              const {data} = await axios(`api/js/${code}.js?rt=${new Date().getTime()}`);

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
