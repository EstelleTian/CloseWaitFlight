<template>
<div class="container">
  <div class="header">
    <h1>{{ header }}</h1>
  </div>
  <div class="generateTime">数据生成时间: {{genTime}}</div>
  <div class="table-container">
    <el-table
      :data="tableData"
      style="width: 100%"
      size="small"
      height="100%"
      :empty-text="emptyText"
      border>
      <el-table-column
        v-for="(item, index) in tableConfig"
        :key="index"
        align="center"
        :label="item.name"
        :prop="item.prop"
        :formatter="item.formatter"
      ></el-table-column>
    </el-table>
  </div>
</div>
</template>

<script>
export default {
  name: 'Container',
  data () {
    let that = this;
    return {
      header: '关舱门等待两小时航班统计',
      genTime:'',
      emptyText:'',
      tableConfig:[
        {
          name: "ID",
          prop: "id",
        },
        {
          name: "航班号",
          prop: "flightid"
        },
        {
          name: "起飞机场",
          prop: "s_depap"
        },
        {
          name: "计划起飞时间",
          prop: "s_deptime",
          formatter:function (rowData) {
            return that.formatter(rowData.s_deptime)
          }
        },
        {
          name: "预计起飞时间",
          prop: "p_deptime",
          formatter:function (rowData) {
            return that.formatter(rowData.p_deptime)
          }
        },
        {
          name: "实际关舱门时间",
          prop: "r_cldtime",
          formatter:function (rowData) {
            return that.formatter(rowData.r_cldtime)
          }
        },
        {
          name: "实际推出时间",
          prop: "r_outtime",
          formatter:function (rowData) {
            return that.formatter(rowData.r_outtime)
          }
        }
        ,
        {
          name: "计划起飞时间",
          prop: "s_arrtime",
          formatter:function (rowData) {
            return that.formatter(rowData.s_arrtime)
          }
        },
        {
          name: "预计起飞时间",
          prop: "p_arrtime",
          formatter:function (rowData) {
            return that.formatter(rowData.p_arrtime)
          }
        },
        {
          name: "实际起飞时间",
          prop: "r_deptime",
          formatter:function (rowData) {
            return that.formatter(rowData.p_arrtime)
          }
        },
        {
          name: "落地机场",
          prop: "s_arrap"
        }
      ],
      tableData:[]
    }
  },
  mounted(){
    this.getTableData();
    setInterval(() => {
      this.getTableData();
    },3000*10)
  },
  methods:{
    getTableData(){
      const url = "/cldNotDep/";
      this.$fetch(url).then(res=>{
        if(res.fme_list.length > 0){
          this.tableData = res.fme_list;
        }else{
          this.emptyText = '暂无数据'
        }
        this.genTime = this.formatterGenTime(res.timestamp);
      }).catch(err=>{
        console.log(err)
        this.emptyText = '获取数据失败'
      })
    },
    formatter(time){
      if(time){
        let date = time.substring(6, 8);
        let hour = time.substring(8, 10);
        let min = time.substring(10, 12);
        return date + '/' + hour + ':' + min;
      }else{
        return time
      }
    },
    formatterGenTime(time){
      var year = time.substring(0, 4);
      var mon = time.substring(4, 6);
      var date = time.substring(6, 8);
      var hour = time.substring(8, 10);
      var min = time.substring(10, 12);
      var str = year + '-' + mon + '-' + date + ' ' + hour + ":" + min;
      return str;
    }
  }
}
</script>

<style>
.container{
  margin: 0;
  padding: 0;
  width: 100%;
  height: 100%;
  display: flex;
  flex-direction: column;
}
.header{
  height: 35px;
  text-align: left;
  padding-left: 50px;
  margin: 10px 0;
}
.generateTime{
  text-align: right;
  padding:0 50px;
  margin: 10px 0;
}
.table-container{
  flex: 1;
  margin: 0 50px 20px;
  border: 1px solid #ccc;
  border-radius: 5px;
  height: 1px;
  overflow: hidden;
}
h1, h2 {
  font-weight: bold;
  margin: 0;
}
</style>
