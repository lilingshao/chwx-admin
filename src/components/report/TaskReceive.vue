<template>
  <div>
    <el-container>
      <el-header style="padding: 0px;display:flex;justify-content:space-between;align-items: center">
        <div style="align-content: center;font-size: 12px" >
              <span class="demonstration">从</span>
              <el-date-picker
                v-model="emp.ptime"
                value-format="yyyy-MM-dd"
                size="mini"
                type="date"
                placeholder="选择日期">
              </el-date-picker>
              <span class="demonstration">到</span>
              <el-date-picker
                v-model="emp.endtime"
                type="date"
                value-format="yyyy-MM-dd"
                size="mini"
                placeholder="结束日期">
              </el-date-picker>
           <el-select v-model="emp.typeBig" clearable  size="mini" placeholder="请选择任务类型" >
            <el-option
              v-for="item in typeBig"
              :key="item.id"
              :label="item.typeName"
              :value="item.id">
            </el-option>
          </el-select>
            <el-button icon="el-icon-search" type="primary" size="mini" @click="loadReports">搜索</el-button>
        </div>
      </el-header>
      <el-main style="padding-left: 0px;padding-top: 0px">
        <div>
          <transition name="slide-fade">
          </transition>
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
              width="107"
              align="left"
              label="任务名称">
            </el-table-column>
            <el-table-column
              prop="typeName"
              width="150"
              align="left"
              label="任务类别">
            </el-table-column>
            <el-table-column
              prop="address"
              width="140"
              label="链接">
              <template slot-scope="scope">
                <el-button type="text" @click="changePage(scope.row.address)">{{scope.row.address}}</el-button>
              </template>
            </el-table-column>
            <el-table-column
              width="100"
              prop="username"
              label="发布人">
            </el-table-column>
            <el-table-column
              prop="ptime"
              width="120"
              label="发布时间">
            </el-table-column>
            <el-table-column
              prop="endtime"
              width="120"
              label="结束时间">
            </el-table-column>
            <el-table-column
              prop="status"
              width="100"
              align="left"
              label="状态">
              <template slot-scope="scope">
                <span v-if="scope.row.status=='0'" style="color: coral">正在进行</span>
                <span v-if="scope.row.status=='1'" style="color: darkgrey">已结束</span>
              </template>
            </el-table-column>
            <el-table-column
              prop="score"
              width="100"
              label="任务分">
            </el-table-column>
            <el-table-column
              prop="number"
              width="80"
              align="left"
              label="下发数">
            </el-table-column>
            <el-table-column
              prop="number2"
              align="left"
              width="80"
              label="完成数">
            </el-table-column>
            <el-table-column
              label="操作"
              width="195"
              align="center">
              <template slot-scope="scope">
                <el-button type="success" @click="See(scope.row)" style="padding: 3px 4px 3px 4px;margin: 2px"
                           size="mini" plain>查看截图
                </el-button>
                <el-upload
                  class="upload-demo"
                  action="/api/file/upload"
                  :on-remove="handleRemove"
                  :on-success="handleSuccess"
                  :before-remove="beforeRemove"
                  multiple
                  :limit="2"
                  :on-exceed="handleExceed"
                  >
                  <el-button size="mini" style="padding: 3px 4px 3px 4px;margin: 2px" type="primary" plain>点击上传</el-button>
                </el-upload>

              </template>
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
       // fileList: [{name: 'food.jpeg', url: 'https://fuss10.elemecdn.com/3/63/4e7f3a15429bfda99bce42a18cdd1jpeg.jpeg?imageMogr2/thumbnail/360x360/format/webp/quality/100'}, {name: 'food2.jpeg', url: 'https://fuss10.elemecdn.com/3/63/4e7f3a15429bfda99bce42a18cdd1jpeg.jpeg?imageMogr2/thumbnail/360x360/format/webp/quality/100'}],
        emps: [],
        typeBig: [],
        keywords: '',
        ptime: '',
        beginDateScope: '',
        faangledoubleup: 'fa-angle-double-up',
        faangledoubledown: 'fa-angle-double-down',
        dialogTitle: '',
        multipleSelection: [],
        depTextColor: '#c0c4cc',
        nations: [],
        politics: [],
        positions: [],
        joblevels: [],
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
          typeBig: [],
          id: '',
          name: '',
          typeName: '',
          address: '',
          username: '',
          ptime: '',
          endtime: '',
          status: '',
          score: '',
          number: '',
          number2: '',
          address: '',

        },
        rules: {
        }
      };
    },
    mounted: function () {
      this.initDate();
      this.loadTaskTypeName();
      this.loadEmps();
    },
    methods: {
      changePage(url){
        this.$router.push({path:url});
        console.log(url);
      },
      handleSuccess(response, file, fileList){
        console.log("response  "+JSON.stringify(response));
        if (response.status == 200 ){
          this.$message.success(response.msg);
        } else {
          this.$message.error(response.msg);
        }
        this.loadEmps();
        console.log("file  "+JSON.stringify(file ));
        console.log("fileList  "+JSON.stringify(fileList));
      },
      handleRemove(file, fileList) {
        console.log(file, fileList);
      },
      handlePreview(file) {
        console.log(file);
      },
      handleExceed(files, fileList) {
        this.$message.warning(`当前限制选择 3 个文件，本次选择了 ${files.length} 个文件，共选择了 ${files.length + fileList.length} 个文件`);
      },
      beforeRemove(file, fileList) {
        return this.$confirm(`确定移除 ${ file.name }？`);
      },

      exportEmps(){
       // var iframe = document.createElement("iframe");
       // iframe.style.display = 'none';
       // iframe.src = "/employee/basic/exportEmp";
       // iframe.onload=function () {
       //   document.body.removeChild(iframe);
       // }
       // document.body.appendChild(iframe);
        window.open("/employee/basic/exportEmp", "_parent");
      },
      cancelSearch(){
        this.advanceSearchViewVisible = false;
        this.emptyEmpData();
        this.beginDateScope = '';
        this.loadEmps();
      },

      handleSelectionChange(val) {
        this.multipleSelection = val;
      },
      currentChange(currentChange){
        this.currentPage = currentChange;
        this.loadEmps();
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
      },
      loadReports(){
        var _this = this;
        this.tableLoading = true;
        this.getRequest("/api/report/getReceive?page=" + this.currentPage + "&limit=10&type="+this.emp.typeBig + "&ptime=" + this.emp.ptime + "&endtime=" + this.emp.endtime).then(resp=> {
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
        this.getRequest("/api/report/getReceive?page=" + this.currentPage + "&limit=10&type="+this.emp.typeBig + "&ptime=" + this.emp.ptime + "&endtime=" + this.emp.endtime).then(resp=> {
          this.tableLoading = false;
          if (resp && resp.status == 200) {
            var data = resp.data.data;
            _this.emps = data.rows;
            _this.totalCount = data.total;
          }
        })
      },
      loadTaskTypeName(){
        var _this = this;
        this.tableLoading = true;
        this.getRequest("api/report/getReportTypeName").then(resp => {
          if(resp && resp.status == 200){
              _this.typeBig = resp.data.data;
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
      See(row){
        console.log(row)
        window.location.href = row.address;
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
</style>
