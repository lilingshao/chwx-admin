<template>
  <div>
    <el-container>
      <el-header style="padding: 0px;display:flex;justify-content:space-between;align-items: center">
        <div style="align-content: center;font-size: 12px" >
              <el-row>
            <el-col :span=7>
              <span class="demonstration">从</span>
              <el-date-picker
                v-model="emp.ptime"
                value-format="yyyy-MM-dd"
                size="mini"
                type="date"
                placeholder="选择日期">
              </el-date-picker>
            </el-col>
          <el-col :span=6 :pull="3">
            <span class="demonstration">到</span>
            <el-date-picker
              v-model="emp.endtime"
              type="date"
              value-format="yyyy-MM-dd"
              size="mini"
              placeholder="结束日期">
            </el-date-picker>
          </el-col>
            <el-col :span=6 :pull="5" >
              <treeselect style="height: 28px"  size="mini" v-model="emp.deptids" :multiple="true" :options="depts" placeholder="请选择地区" :normalizer="normalizer"/>
            </el-col>
          <el-col :span=3 :pull="5">
            <el-input style="width: 200px" placeholder="请输入任务名称" size="mini" v-model="emp.name" clearable></el-input>
          </el-col>
            <el-col :span=2 :pull="5" >
              <el-button icon="el-icon-search" type="primary" size="mini" @click="loadReports">搜索</el-button>
            </el-col>
              </el-row>

        </div>

      </el-header>
      <div style=" text-align: left">
        <el-dialog title="任务执行详情" :visible.sync="dialogFormVisible" style="width: 100%;height: 100%">
          <el-table
            :data="tableData"
            border
            style="width: 100%">
            <el-table-column
              prop="index"
              label="编号"
              width="180">
            </el-table-column>
            <el-table-column
              prop="name"
              label="执行人"
              width="180">
            </el-table-column>
            <el-table-column
              prop="number"
              label="执行条数">
            </el-table-column>
            <el-table-column
              prop="score"
              label="得分">
            </el-table-column>
            <el-table-column
              prop="show"
              label="查看图片">
              <template slot-scope="scope">
                <el-button type="text" style="color: lightseagreen" @click="showPictuer(scope.row)">
                  查看
                </el-button>
              </template>
            </el-table-column>
          </el-table>
          <div style="display: flex;justify-content: space-between;margin: 2px">
            <el-button type="text" size="mini" v-if="emps.length>0" disabled>
            </el-button>
            <el-pagination
              background
              :page-size="10"
              :current-page="page"
              @current-change="pageChange"
              layout="prev, pager, next"
              :total="total">
            </el-pagination>
          </div>
        </el-dialog>
      </div>

      <div style=" text-align: left" class="my">
        <el-dialog v-bind:title="title"  :visible.sync="dialogFormVisible1" style="width: 100%;height: 100%;">
         <!-- <el-row v-model="items2">
            <ul>
              <li>任务类型:  {{items2.report.typeName}}</li>
              <li>发布时间:  {{items2.report.ptime}}</li>
            </ul>
            <ul>
              <li>查看人数:{{items2.report.readNumber}}</li>
              <li>查看率:{{items2.report.readNumber}}%********</li>
              <li>执行人数:{{items2.report.zhixPersonNumber}}</li>
              <li>执行率:</li>
            </ul>
            <ul>
              <li>发布数量:{{items2.report.pnumber}}</li>
              <li>发布人数:{{items2.userlist.length}}</li>
              <li>执行数量:</li>
              <li>完成率:</li>
            </ul>
          </el-row>-->
          <el-row>
            <div class="div-title" ><span >地区占比</span></div>
          </el-row>
            <el-row>
              <el-col :span="12">
                <div id="pie" style="width: 450px;height: 500px"></div>
              </el-col>
              <el-col :span="12">
                <div class="div-title-right"  ><span class="span-title">地区完成数量排行榜TOP10</span></div>

              </el-col>
            </el-row>

          <el-row>
            <div class="div-title"  ><span>个人排名数量</span></div>
          </el-row>
          <el-row>
            <el-col :span="12">
              <div id="line" style="width: 450px;height: 500px"></div>
            </el-col>
            <el-col :span="12" >
              <div class="div-title-right" ><span class="span-title">个人数量排行榜TOP10</span></div>
              <ul class="example-1" v-for="item in userlist">
                <li >{{item.name}}</li>
                <li >({{item.number}})</li>
              </ul>

            </el-col>

          </el-row>

          <el-row>
            <div class="div-title" ><span>个人积分排名</span></div>
          </el-row>
          <el-row>
            <el-col :span="12">
              <div id="line2" :style="{width: '450px', height: '500px'}"></div>
            </el-col>
            <el-col :span="12">
              <div class="div-title-right" ><span class="span-title">个人积分排行榜TOP10</span></div>
              <ul id="example-2" v-for="item in jflist" >
                <li >{{ item.name}}</li>
                <li >({{ item.score}})</li>
              </ul>

            </el-col>
          </el-row>

          <el-row>
            <div class="div-title" ><span style="text-align: center">响应速度排名</span></div>
          </el-row>
          <el-row>
            <el-col :span="12">
              <div id="line3" :style="{width: '450px', height: '500px'}"></div>
            </el-col>
            <el-col :span="12">
              <div class="div-title-right" ><span class="span-title">响应速度排行榜TOP10</span></div>
              <ul id="example-4" v-for="item in lvlist">

              </ul>
            </el-col>
          </el-row>
        </el-dialog>
      </div>

      <el-main style="padding-left: 0px;padding-top: 0px">
        <div>
          <el-table
            :data="emps"
            v-loading="tableLoading"
            border
            stripe
            @selection-change="handleSelectionChange"
            size="mini"
            style="width: 100%">
            <el-table-column
              prop="index"
              align="left"
              fixed
              label="编号"
              width="50">
            </el-table-column>
            <el-table-column
              prop="name"
              align="left"
              fixed
              label="任务名称"
              width="180">
            </el-table-column>
            <el-table-column
              prop="typeName"
              width="110"
              align="left"
              label="任务类别">
            </el-table-column>
            <el-table-column
              prop="address"
              width="150"
              label="链接">
              <template slot-scope="scope">
      <!--          <el-button type="text" style="color: darkgray" @click="showPictuer(scope.row)">
                  {{scope.row.address}}
                </el-button>-->
                <!--<span @click="See(scope.row)">{{scope.row.address}}</span>-->
                <a  target="_blank" style="color: cadetblue;" :href="scope.row.address" >{{scope.row.address}}</a>
              </template>
            </el-table-column>
            <el-table-column
              width="100"
              prop="username"
              label="发布人">
            </el-table-column>
            <el-table-column
              prop="ptime"
              width="170"
              align="left"
              label="发布时间">
            </el-table-column>
            <el-table-column
              prop="endtime"
              width="170"
              align="left"
              label="结束时间">
            </el-table-column>
            <el-table-column
              prop="number3"
              width="80"
              align="left"
              label="下发人数">

            </el-table-column>

            <el-table-column
              width="70"
              prop="number"
              label="总数">
            </el-table-column>
            <el-table-column
              width="80"
              prop="number2"
              label="完成数">
            </el-table-column>

            <el-table-column
              prop="score"
              label="操作"
              width="180">
              <template slot-scope="scope">
                <el-button type="text" @click="showPic(scope.row)" style="color: gold" >图片查看</el-button>
                <el-button type="text" @click="viewChange(scope.row.id)" style="color: gold" >详情分析</el-button>
              </template>
            </el-table-column>

          </el-table>


          <div style="display: flex;justify-content: space-between;margin: 2px">
            <el-button type="text" size="mini" v-if="emps.length>0" >
            </el-button>
            <el-pagination
              background
              :page-size="10"
              :current-page="currentPage"
              @current-change="currentChange"
              layout="prev, pager, next"
              :total="totalCount">
            </el-pagination>
          </div>

          <div style="text-align: left">
            <el-dialog
              :title="dialogTitle"
              style="padding: 0px;"
              :close-on-click-modal="false"
              :visible.sync="dialogVisible"
              width="77%">
                  <div style="display:inline;padding-right:10px"  v-for ="item in pictures" :key="item.id">
                  <img :src="item.address" width="100" height="100" alt="">
                  </div>
              <span slot="footer" class="dialog-footer">
              <el-button size="mini" @click="cancelEidt">关 闭</el-button>
              </span>
            </el-dialog>
          </div>
        </div>
      </el-main>
    </el-container>
  </div>
</template>
<script>

  import Treeselect from '@riophae/vue-treeselect'
  // import the styles
  import '@riophae/vue-treeselect/dist/vue-treeselect.css'

  export default {
    components: {Treeselect},
    data() {
      return {
        normalizer(node) {
          return {
            id: node.id,
            label: node.name,
            children: node.children,
          }
        },
        jflist: [],
        userlist: [],
        lvlist: [],
        title: '',
        pieData: [],
        pieDeptData:[],
        barData: [],
        barDataNum: [],
        barData2: [],
        barDataNu2: [],
        barData3: [],
        barDataNum3: [],
        row: {},
        tableData: [],
        dialogFormVisible: false,
        dialogFormVisible1: false,
        data: [],
        depts: [],
        emps: [],
        pictures:[],
        ptime: '',
        faangledoubleup: 'fa-angle-double-up',
        faangledoubledown: 'fa-angle-double-down',
        dialogTitle: '',
        multipleSelection: [],
        depTextColor: '#c0c4cc',
        totalCount: -1,
        currentPage: 1,
        page: 1,
        total: -1,
        deps: [],

        defaultProps: {
          label: 'name',
          children: 'children'
        },
        dialogVisible: false,
        tableLoading: false,
        emp: {
          type: [],
          number: '',
          deptids: null,
          ptime: '',
          endtime: '',
          name: '',
          address: '',
        },
      };
    },
    mounted: function () {

      this.initData();
      this.initDate();

    },
    methods: {
      showPictuer(row) {
      console.log(row.id);
      this.getRequest(
        "api/report/getPictureAddress?id=" + row.id
      ).then(resp => {
        this.picures=[];
        this.dialogVisible = true;
        // console.log(resp.data.data.rows);
        if (resp && resp.data.status == 200) {
          var data = resp.data.data;
          this.picures= data.rows;
        }
      });
    },
      drawPie(){
        // 基于准备好的dom，初始化echarts实例
        let pie = this.$echarts.init(document.getElementById('pie'))
        pie.setOption({
          title : {
            text: '地区完成数量排行榜TOP:10',
            subtext: '',
            x:'center'
          },
          tooltip : {
            trigger: 'item',
            formatter: "{a} <br/>{b} : {c} ({d}%)"
          },
          legend: {
            orient: 'vertical',
            left: 'right',
            data: this.pieDeptData
          },
          series : [
            {
              name: '',
              type: 'pie',
              radius : '50%',
              center: ['50%', '60%'],
              data: this.pieData,
              itemStyle: {

                emphasis: {
                  shadowBlur: 10,
                  shadowOffsetX: 0,
                  shadowColor: 'rgba(0,0, 0, 0.5)'
                }
              }
            }
          ]
        });
      },


      drawBar(){
        // 基于准备好的dom，初始化echarts实例
        let myChart = this.$echarts.init(document.getElementById('line'))
        // 绘制图表
        myChart.setOption({
          title: { text: '个人排名TOP10',x:'center' },
          tooltip: {
            trigger: 'axis',
            axisPointer : {            // 坐标轴指示器，坐标轴触发有效
              type : 'shadow'        // 默认为直线，可选为：'line' | 'shadow'
            }
          },
          grid: {  //
            y2: 140
          },
          xAxis: {
            type: 'category',
            data: this.barData,
            axisLabel:{
              interval:0,//横轴信息全部显示
              rotate:-30,//-30度角倾斜显示
            }
          },
          yAxis: {
            type: 'value',
            max: 10
          },
          series: [{
            name: '个人排名',
            type: 'bar',
            data: this.barDataNum,
            barCategoryGap: 20
          }]
        });
      },

      drawBar2(){
        // 基于准备好的dom，初始化echarts实例
        let myChart = this.$echarts.init(document.getElementById('line2'))
        // 绘制图表
        myChart.setOption({
          color: ['#3398DB'],
          title: { text: '个人积分TOP10' ,x:'center' },
          tooltip : {
            trigger: 'axis',
            axisPointer : {            // 坐标轴指示器，坐标轴触发有效
              type : 'shadow'        // 默认为直线，可选为：'line' | 'shadow'
            }
          },
          xAxis : [
            {
              type : 'category',
              data : this.barData,
              axisLabel:{
                interval:0,//横轴信息全部显示
                rotate:-30,//-30度角倾斜显示
              }
            }
          ],
          grid: {
            y2: 140,//和上面搭配   axisLabel:
            left: '3%',
            right: '4%',
            bottom: '3%',
            containLabel: true
          },
          yAxis: {
            max: 10,
            type : 'value'
          },
          series: [{
            barWidth : 30,//柱图宽度
            //barMaxWidth:30,//最大宽度
            name: '个人积分',
            type: 'bar',
            barWidth: '60%',
            data: this.barDataNum2
            //data:[1, 5, 2, 4, 3, 3, 2]
          }]
        });
      },
      drawBar3(){
        // 基于准备好的dom，初始化echarts实例
        let myChart = this.$echarts.init(document.getElementById('line3'))
        // 绘制图表
        myChart.setOption({
          title: { text: '响应速度TOP10' ,x:'center' },
          tooltip: {},
          grid: {  //
            y2: 140
          },
          xAxis: {
            data: this.barData3,
            axisLabel:{
              interval:0,//横轴信息全部显示
              rotate:-30,//-30度角倾斜显示
            }
          },
          yAxis: {
            type: 'value',
            max: 10
          },
          series: [{
            name: '响应速度',
            type: 'line',
            data: this.barDataNum3
          }]
        });
      },

/*      See(row){
        window.location.href = row.address;
      },*/
      viewChange(id){
        var _this = this;
        this.dialogFormVisible1 = true;
        this.getRequest("api/report/reportDetail?id="+id).then(resp=>{
          console.log("返回结果  "+JSON.stringify(resp.data));
            if (resp && resp.status == 200){
                _this.title = resp.data.report.name;
                _this.pieData = JSON.parse(resp.data.deptPie);
                _this.pieDeptData = resp.data.deptlist.name;
                _this.barData =resp.data.barAxis;
                console.log("姓名:"+_this.barData);
                _this.barDataNum = resp.data.barAYis;
                console.log("f分数:"+_this.barDataNum);
                _this.barData2 = resp.data.jfbarAxis;
                _this.barDataNum2 = resp.data.jfbarAYis;
                _this.barData3 = resp.data.lvdata;
                _this.barDataNum3 = resp.data.lvbarAxis;
                _this.userlist = resp.data.userlist;
                _this.jflist = resp.data.jflist;
                console.log("*** "+resp.data);
                //_this.lvlist = resp.data.lvlist;
              console.log("items   "+JSON.stringify(_this.userlist));
              this.drawPie();
              this.drawBar();
              this.drawBar2();
              this.drawBar3();
            }
        })
      },

      showPic(row){
        this.row = row;
        var _this = this;
        this.tableLoading = true;
        this.getRequest("api/report/getReportUser?id="+this.row.id+"&page="+this.page+"&limit=10"+"&ptime="+this.emp.ptime+"&endtime="+this.emp.endtime).then(resp=>{
            if (resp && resp.status == 200 ) {
              var data = resp.data.data;
              _this.tableData = data.rows;
              _this.total = data.total;
              _this.tableLoading = false;
          }
        })
        this.dialogFormVisible = true;
      },
      handleSelectionChange(val) {
        this.multipleSelection = val;
      },
      currentChange(currentChange){
        this.currentPage = currentChange;
        this.loadEmps();
      },
      pageChange(pageChange){
        console.log("************* +++++"+pageChange);
        this.page = pageChange;
        this.showPic(this.row);
        console.log("此处有个小问题!!!!!!!");
      },
    loadReports(){
        this.loadEmps();
     /* var _this = this;
      this.tableLoading = true;
      if(this.emp.deptids == null){
          this.emp.deptids = -1;
      }
      this.getRequest("api/report/getReport?page="+this.currentPage+"&limit=10"+"&ptime="+this.emp.ptime+"&endtime="+this.emp.endtime+"&deptid="+this.emp.deptids+"&name="+this.emp.name).then(resp=> {
        this.tableLoading = false;
        //console.log("***********"+JSON.stringify(resp));
        if (resp && resp.data.status == 200) {
          var data = resp.data.data;
          _this.emps = data.rows;
          _this.totalCount = data.total;
        }
      })*/
      },
      loadEmps(){
        var _this = this;
        this.tableLoading = true;
        if(this.emp.deptids == null){
          this.emp.deptids = -1;
        }
        this.getRequest("api/report/getReport?page="+this.currentPage+"&limit=10"+"&ptime="+this.emp.ptime+"&endtime="+this.emp.endtime+"&deptid="+this.emp.deptids+"&name="+this.emp.name).then(resp=> {
              this.tableLoading = false;
          if (resp && resp.data.status == 200) {
            var data = resp.data.data;
            _this.emps = data.rows;
           // console.log("***   "+JSON.stringify(_this.emps));
            _this.totalCount = data.total;
          }
        })
      },
      initData(){
        var _this =this;
        this.getRequest("/api/ndept/deptTree/1").then(resp=>{
          var data = resp.data;
          if (resp && resp.status ==200){
            _this.depts = data.data;
            _this.data = data.data;
          }
        })
      },
      initDate(){
        let nowDate = new Date();
        let year = nowDate.getFullYear();
        let month = nowDate.getMonth() + 1;
        let day = nowDate.getDate();
        let endTime = `${year}-${month}-${day}`;
        let lastMonth = month -1;
        if(lastMonth<10){
          lastMonth = "0"+lastMonth;
        }
        this.emp.endtime = endTime;//当前时间点
        this.emp.ptime = `${year}-${lastMonth}-${day}`;
        this.loadReports();
      },
      emptyEmpData(){
        this.emp = {
          ptime: '',
          endtime: '',
          deptids: '',
          name: '',
          address: '',
          deptids: null,
        }
      }
    }
  };
</script>
<style>

  a:link {
    font-size: 12px;
    color: #000000;
    text-decoration: none;
  }
  a:visited {
    font-size: 12px;
    color: #000000;
    text-decoration: none;
  }
  a:hover {
    font-size: 12px;
    color: #999999;
    text-decoration: underline;
  }
  .span-title{
    text-align: center;
    display: block;
  }
  .div-title-right{
    width:100%;
    font-size: 18px;
    font-family: 微软雅黑;
    font-weight: bold;
  }
  .my ul{
    width: 500px;
    display:inline;
    white-space: nowrap;
  }
  .my li{
    width: 100px;   /*如果显示三列 则width改为70px*/
    float: left;
    display: block;
    padding: 0px 0px 0px 5px;
    display: inline-block;
    white-space:nowrap;
  }
/*  .example-1{
    width: 600px;
    float:left;
    color:#000;
  }
  .example-1 li{
    float:left;
    list-style: none;
  }
  .example-1 li{

  }*/

  .div-title{
    width: 100%;
    height: 28px;
    font-size: 18px;
    background-color: lightseagreen;
    font-weight: normal;
    font-family: 微软雅黑;
  }
  .vue-treeselect__control {
    padding-left: 5px;
    padding-right: 5px;
    display: table;
    table-layout: fixed;
    width: 100%;
    height: 28px;

  }
</style>
