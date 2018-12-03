<template>
  <div>
    <el-container>
      <el-header style="padding: 0px;display:flex;justify-content:space-between;align-items: center;font-size: 12px">

        <div style="width: 300px">
                <el-button plain size="mini" @click="last3Month">{{this.month.id-3}}月</el-button>
                <el-button plain size="mini" @click="last2Month">{{this.month.id-2}}月</el-button>
                <el-button plain size="mini" @click="lastMonth">{{this.month.id-1}}月</el-button>
                <el-button plain size="mini" @click="nowMonth">当月</el-button>

        </div>
        <div style="width: 800px">
          <span class="demonstration">从</span>
          <el-date-picker
            v-model="emp.ptime"
            value-format="yyyy-MM-dd"
            size="mini"
            type="date"
            style="width: 200px"
            placeholder="选择日期">
          </el-date-picker>
          <span class="demonstration">到</span>
          <el-date-picker
            v-model="emp.endtime"
            type="date"
            style="width: 200px"
            value-format="yyyy-MM-dd"
            size="mini"
            placeholder="结束日期">
          </el-date-picker>
          <!--<el-input  style="width: 200px" placeholder="请输入任务名称" size="mini" v-model="emp.name" clearable></el-input>-->
        </div >
        <div style="width: 200px">
        </div>
        <treeselect style="width: 200px;" v-model="emp.deptids" :multiple="true" :options="depts" placeholder="请选择地区" :normalizer="normalizer"/>
        <el-button icon="el-icon-search" type="primary" size="mini" @click="loadReports">搜索</el-button>

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
              width="80">
            </el-table-column>
            <el-table-column
              prop="ptime"
              label="上报时间"
              width="180">
            </el-table-column>
            <el-table-column
              prop="value"
              label="任务类型"
              width="200">
            </el-table-column>
            <el-table-column
              prop="number"
              label="执行条数"
              width="130">
            </el-table-column>
            <el-table-column
              prop="score"
              label="得分"
              width="130">
            </el-table-column>
            <el-table-column
              prop="markto"
              label="批示"
              width="198">
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
              width="225">
            </el-table-column>
            <el-table-column
              prop="username"
              align="left"
              fixed
              label="组员"
              width="225">
            </el-table-column>
            <el-table-column
              prop="value"
              width="225"
              align="left"
              label="所属部门">
            </el-table-column>

            <el-table-column
              width="225"
              prop="number"
              label="执行条数">
              <template slot-scope="scope">
                <span v-if="scope.row.number==null" >0</span>
                <span v-if="scope.row.number!=null" >{{scope.row.number}}</span>
              </template>
            </el-table-column>
            <el-table-column
              width="225"
              prop="score"
              label="得分">
              <template slot-scope="scope">
                <span v-if="scope.row.score==null" >0</span>
                <span v-if="scope.row.score!=null" >{{scope.row.score}}</span>
              </template>
            </el-table-column>

            <el-table-column
              prop="operation"
              label="操作"
              width="205">
              <template slot-scope="scope">
                <el-button type="text" @click="SeeDetails(scope.row)" style="color: green" >查看详情</el-button>
              </template>
            </el-table-column>

          </el-table>


          <div style="display: flex;justify-content: space-between;margin: 2px">
            <el-button type="text" size="mini" v-if="emps.length>0" disabled>
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
        month: [{id:''}],
        tableData1: [],
        tableData: [],
        dialogFormVisible: false,
        data: [],
        depts: [],
        emps: [],
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
        advanceSearchViewVisible: false,
        showOrHidePop: false,
        showOrHidePop2: false,
        emp: {
          type: [],
          number: '',
          deptids: null,
          ptime: '',
          endtime: '',
          address: '',
        },
        auditForm: {
          name: '',
          id: '',//任务ID
          status: [],//更新审核状态
          mark: '',//描述
          score: '',//分值
          type: '',//当前审核状态
        },
        formLabelWidth: '120px'
      };
    },
    mounted: function () {
      this.initData();
      this.initDate();
      this.loadEmps();
    },
    methods: {
      nowMonth(){
        let nowDate = new Date();
        let year = nowDate.getFullYear();
        let month = nowDate.getMonth() + 1;
        let day = nowDate.getDate();
        if(month < 10){
          month = "0"+month;
        }
        if(day < 10){
          day = "0"+day;
        }
        let endTime = `${year}-${month}-${day}`;
        this.emp.endtime = endTime;//当前时间点
        this.emp.ptime = `${year}-${month}-1`;
        this.loadReports();
      },
      lastMonth(){//上月
        let nowDate = new Date();
        let year = nowDate.getFullYear();
        let month = nowDate.getMonth() + 1;
        let lastMonth = month -1;//上月
        if(lastMonth < 10){
          lastMonth = "0"+lastMonth;
        }
        let flag = new Date(year,lastMonth,0);
        console.log("最后一天"+flag.getDate());
        this.emp.ptime = `${year}-${lastMonth}-01`;
        this.emp.endtime =`${year}-${lastMonth}-${flag.getDate()}`;
        this.loadReports();
      },
      last2Month(){//上上月
        let nowDate = new Date();
        let year = nowDate.getFullYear();
        let month = nowDate.getMonth() + 1;
        let lastMonth = month -2;//上上月
        if(lastMonth < 10){
          lastMonth = "0"+lastMonth;
        }
        let flag = new Date(year,lastMonth,0);
        console.log("最后一天"+flag.getDate());
        this.emp.ptime = `${year}-${lastMonth}-01`;
        this.emp.endtime =`${year}-${lastMonth}-${flag.getDate()}`;
        this.loadReports();
      },
      last3Month(){//上上上月
        let nowDate = new Date();
        let year = nowDate.getFullYear();
        let month = nowDate.getMonth() + 1;
        let lastMonth = month -3;//上上上月
        if(lastMonth < 10){
          lastMonth = "0"+lastMonth;
        }
        let flag = new Date(year,lastMonth,0);
        console.log("最后一天"+flag.getDate());
        this.emp.ptime = `${year}-${lastMonth}-01`;
        this.emp.endtime =`${year}-${lastMonth}-${flag.getDate()}`;
        this.loadReports();
      },

      cancelAudit(){
        this.dialogFormVisible = false;
        this.auditForm.mark = '';
        this.auditForm.status = '';
      },
      showAdvanceSearchView(){
        this.advanceSearchViewVisible = !this.advanceSearchViewVisible;
        this.keywords = '';
        if (!this.advanceSearchViewVisible) {
          this.emptyEmpData();
          this.ptime = '';
          this.loadEmps();
        }
      },
      handleSelectionChange(val) {
        this.multipleSelection = val;
      },
      keywordsChange(val){
        if (val == '') {
          this.loadEmps();
        }
      },
      searchEmp(){
        this.loadEmps();
      },
      currentChange(currentChange){
        this.currentPage = currentChange;
        this.loadEmps();
      },
      pageChange(pageChange){
        console.log("************* +++++"+pageChange);
        this.page = pageChange;
        console.log("此处有个小问题!!!!!!!");
      },
    loadReports(){
      var _this = this;
      this.tableLoading = true;
      this.getRequest("api/reportTask/reportCountByUser?isAu=1&page="+this.currentPage+"&limit=10"+"&ptime="+this.emp.ptime+"&endtime="+this.emp.endtime+"&deptid="+this.emp.deptids).then(resp=> {
        this.tableLoading = false;
        //console.log("***********"+JSON.stringify(resp));
        if (resp && resp.data.status == 200) {
          var data = resp.data.data;
          _this.emps = data.rows;
          _this.totalCount = data.total;
        }
      })
      },
      loadEmps(){
        var _this = this;
        this.tableLoading = true;
        if (this.emp.deptids == null ){
          this.emp.deptids = -1;
        }
        this.getRequest("api/reportTask/reportCountByUser?isAu=&page="+this.currentPage+"&limit=10"+"&ptime="+this.emp.ptime+"&endtime="+this.emp.endtime+"&deptid="+this.emp.deptids).then(resp=> {
          this.tableLoading = false;
          //console.log("***********"+JSON.stringify(resp));
          if (resp && resp.data.status == 200) {
            var data = resp.data.data;
            _this.emps = data.rows;
            _this.totalCount = data.total;
          }
        })
      },
      SeeDetails(row){
        console.log("****  "+JSON.stringify(row))
        var _this = this;
        this.tableLoading = true;
        this.getRequest("api/reportTask/taskReportDetailByUser?userid="+row.id+"&page="+this.currentPage+"&limit=10"+"&ptime="+this.emp.ptime+"&endtime="+this.emp.endtime).then(resp=>{
          if (resp && resp.status == 200 ) {
            var data = resp.data.data;
            _this.tableData = data.rows;
            _this.total = data.total;
          }
        })
        this.tableLoading = false;
        this.dialogFormVisible = true;
      },
      cancelEidt(){
        this.dialogVisible = false;
        this.emptyEmpData();
      },
      showDepTree(){
        this.showOrHidePop = !this.showOrHidePop;
      },
      showDepTree2(){
        this.showOrHidePop2 = !this.showOrHidePop2;
      },
      handleNodeClick(data) {
        this.emp.departmentName = data.name;
        this.emp.deptids = data.id;
        this.showOrHidePop = false;
        this.depTextColor = '#606266';
      },
      handleNodeClick2(data) {
        this.emp.departmentName = data.name;
        this.emp.deptids = data.id;
        this.showOrHidePop2 = false;
        this.depTextColor = '#606266';
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
        this.emp.endtime = endTime;//当前时间点
        this.emp.ptime = `${year}-${month}-1`;
        this.month.id = month;
        console.log("***   "+this.month.id);
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

  .el-row {
    margin-bottom: 20px;
    &:last-child {
      margin-bottom: 0;
     }
  }
  .el-col {
    border-radius: 4px;
  }
  .bg-purple-dark {
    background: #99a9bf;
  }
  .bg-purple {
    background: #d3dce6;
  }
  .bg-purple-light {
    background: #e5e9f2;
  }
  .grid-content {
    border-radius: 4px;
    min-height: 36px;
  }
  .row-bg {
    padding: 10px 0;
    background-color: #f9fafc;
  }

  .el-dialog__body {
    padding-top: 0px;
    padding-bottom: 0px;
  }

  .slide-fade-enter-active {
    transition: all .8s ease;
  }

  .slide-fade-leave-active {
    transition: all .8s cubic-bezier(1.0, 0.5, 0.8, 1.0);
  }

  .slide-fade-enter, .slide-fade-leave-to {
    transform: translateX(10px);
    opacity: 0;
  }
/*  .mini-tree-node-hover .mini-tree-nodeshow
  {
     padding:0;
     padding-left:1px;
     padding-right:2px;
  }


  .mini-tree-selectedNode
  {
     padding:0;
     padding-left:1px;
     padding-right:2px;
     border:1px solid #A9ACB5;
     background:#1E90FF;
     zoom:1;
  }
  .mini-tree-selectedNode:hover {
     background-color: #DEDEDE;
    }
 .mini-tree-nodetext
  {
     width: 2000%;
     height:24px;
     line-height:18px;
      +line-height:18px;
      vertical-align:middle;
      _vertical-align:top;
      display:inline-block;
      padding-left:303px;
      padding-right:3px;
      white-space:nowrap;
      margin-left: -300px;
  }


  .mini-tree-nodetext a
  {
     text-decoration:none;
     color:#000;
     outline:none;
     display:inline-block;
     margin-bottom:0px;
     margin-top:0px\9\0;
      +line-height:0px;
      _margin-top:2px;
      width:300%;
  }*/
</style>
