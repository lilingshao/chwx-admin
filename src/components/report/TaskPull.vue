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
          <el-input  style="width: 200px" placeholder="请输入任务名称" size="mini" v-model="emp.name" clearable></el-input>
          <el-button icon="el-icon-search" type="primary" size="mini" @click="loadReports">搜索</el-button>
        </div >
        <div style="margin-left: 5px;margin-right: 20px;display: inline">
          <el-upload
            :show-file-list="false"
            accept="application/vnd.ms-excel"
            action="/employee/basic/importEmp"
            :on-success="fileUploadSuccess"
            :on-error="fileUploadError" :disabled="fileUploadBtnText=='正在导入'"
            :before-upload="beforeFileUpload" style="display: inline">
            <el-button size="mini" type="success" :loading="fileUploadBtnText=='正在导入'">
              <i class="fa fa-lg fa-level-up" style="margin-right: 5px"></i>
              {{fileUploadBtnText}}
            </el-button>
          </el-upload>
          <el-button type="success" size="mini" @click="exportEmps"><i class="fa fa-lg fa-level-down"
                                                                       style="margin-right: 5px"></i>导出数据
          </el-button>
        </div>
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
              width="200">
            </el-table-column>
            <el-table-column
              prop="address"
              width="180"
              align="left"
              label="任务链接">
              <template slot-scope="scope">
                <!--          <el-button type="text" style="color: darkgray" @click="showPictuer(scope.row)">
                            {{scope.row.address}}
                          </el-button>-->
                <a  target="_blank" style="color: cadetblue;" :href="scope.row.address" >{{scope.row.address}}</a>
              </template>

            </el-table-column>
            <el-table-column
              prop="username"
              label="发布人"
              width="60">
            </el-table-column>
            <el-table-column
              prop="typeName"
              width="100"
              align="left"
              label="任务类型">
            </el-table-column>
            <el-table-column
              prop="ptime"
              width="200"
              align="left"
              label="发布时间">
            </el-table-column>
            <el-table-column
              prop="endtime"
              width="200"
              label="截止时间">
            </el-table-column>
            <el-table-column
              width="80"
              prop="number"
              label="任务数量">
            </el-table-column>
            <el-table-column
              prop="mark"
              width="240"
              label="任务描述">
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
        fileUploadBtnText: '导入数据',
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
      fileUploadSuccess(response, file, fileList){
        if (response) {
          this.$message({type: response.status, message: response.msg});
        }
        this.loadEmps();
        this.fileUploadBtnText = '导入数据';
      },
      fileUploadError(err, file, fileList){
        this.$message({type: 'error', message: "导入失败!"});
        this.fileUploadBtnText = '导入数据';
      },
      beforeFileUpload(file){
        this.fileUploadBtnText = '正在导入';
      },
      handleSelectionChange(val) {
        this.multipleSelection = val;
      },
      currentChange(currentChange){
        this.currentPage = currentChange;
        this.loadEmps();
      },
    loadReports(){
        var _this = this;
        this.tableLoading = true;
      this.getRequest("api/report/getPublish?ptime="+this.emp.ptime+"&endtime="+this.emp.endtime+"&name="+this.emp.name+"&page="+this.currentPage+"&limit=10").then(resp=>{
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
        this.getRequest("api/report/getPublish?ptime="+this.emp.ptime+"&endtime="+this.emp.endtime+"&name="+this.emp.name+"&page="+this.currentPage+"&limit=10").then(resp=>{
       //this.getRequest("api/report/queryorder?page=" + this.currentPage + "&limit=10&name=" + this.keywords ).then(resp=> {
          this.tableLoading = false;
          //console.log("***********"+JSON.stringify(resp));
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
