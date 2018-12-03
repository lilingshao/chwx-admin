<template>
  <div>
    <el-container>
      <el-header style="padding: 0px;display:flex;justify-content:space-between;align-items: center">
        <div style="width: 800px;font-size: 12px">
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
          <el-input  style="width: 200px" placeholder="请输入登录名..." size="mini" v-model="emp.name" clearable></el-input>
          <el-button icon="el-icon-search" type="primary" size="mini" @click="loadSearch">搜索</el-button>
        </div >
      </el-header>
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
              prop="id"
              align="center"
              fixed
              label="编号"
              width="50">
            </el-table-column>

            <el-table-column
              prop="name"
              align="center"
              fixed
              label="操作者"
              width="200">
            </el-table-column>
            <el-table-column
              prop="operType"
              width="400"
              align="center"
              label="操作类型">
            </el-table-column>

            <el-table-column
              prop="operTime"
              width="300"
              align="center"
              label="操作时间">
            </el-table-column>
            <el-table-column
              align="center"
              prop="ip"
              width="250"
              label="IP">
            </el-table-column>
            <el-table-column
              align="center"
              width="200"
              prop="city"
              label="所在城市">
            </el-table-column>

          </el-table>
          <div style="display: flex;justify-content: space-between;margin: 2px">
            <el-button type="text" size="mini" v-if="emps.length>0" :disabled="multipleSelection.length==0">
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
  export default {
    data() {
      return {
        emps: [],
        ptime: '',
        faangledoubleup: 'fa-angle-double-up',
        faangledoubledown: 'fa-angle-double-down',
        dialogTitle: '',
        multipleSelection: [],
        depTextColor: '#c0c4cc',
        totalCount: -1,
        currentPage: 1,
        deps: [],
        defaultProps: {
          label: 'name',
          isLeaf: 'leaf',
          children: 'children'
        },
        dialogVisible: false,
        tableLoading: false,
        advanceSearchViewVisible: false,
        showOrHidePop: false,
        showOrHidePop2: false,
        emp: {
          ptime: '',
          endtime: '',
          name: '',
          address: '',
        },
        rules: {
        }
      };
    },
    mounted: function () {
      this.loadEmps();
    },
    methods: {
      exportEmps(){
        //window.open("/employee/basic/exportEmp", "_parent");
        window.open("/api/report/exportReports?page=&size=&name="+this.keywords, "_parent");
      },

      handleSelectionChange(val) {
        this.multipleSelection = val;
      },
      currentChange(currentChange){
        this.currentPage = currentChange;
        this.loadEmps();
      },
      loadSearch(){
        var _this = this;
        this.tableLoading = true;
        this.getRequest("api/history/list?starttime="+this.emp.ptime+"&endtime="+this.emp.endtime+"&name="+this.emp.name+"&page="+this.currentPage+"&limit=10").then(resp=>{
          this.tableLoading = false;
          if (resp && resp.status == 200) {
            var data = resp.data.data;
            _this.emps = data.rows;
            _this.totalCount = data.total;
          }
        })
      },
      loadEmps(){
        var _this = this;
        this.tableLoading = true;
        this.getRequest("api/history/list?starttime="+this.emp.ptime+"&endtime="+this.emp.endtime+"&name="+this.emp.name+"&page="+this.currentPage+"&limit=10").then(resp=>{
          //this.getRequest("api/report/queryorder?page=" + this.currentPage + "&limit=10&name=" + this.keywords ).then(resp=> {
          this.tableLoading = false;
          console.log("***********"+JSON.stringify(resp));
          if (resp && resp.data.status == 200) {
            var data = resp.data.data;
            _this.emps = data.rows;
            _this.totalCount = data.total;
          }
        })
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
        this.emp.departmentId = data.id;
        this.showOrHidePop = false;
        this.depTextColor = '#606266';
      },
      handleNodeClick2(data) {
        this.emp.departmentName = data.name;
        this.emp.departmentId = data.id;
        this.showOrHidePop2 = false;
        this.depTextColor = '#606266';
      },
      emptyEmpData(){
        this.emp = {
          name: '',
          address: '',
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
</style>

