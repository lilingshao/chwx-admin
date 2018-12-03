<template>
  <div>
    <el-container> 
      <el-header style="padding: 0px;display:flex;justify-content:space-between;align-items: center">
        <div style="margin-left: 5px;margin-right: 20px;display: inline">
          <div class="block">
              <span class="demonstration">从</span>
              <el-date-picker
                v-model="reportForm.starttime"
                value-format="yyyy-MM-dd HH:mm:ss"
                size="mini"
                style="width: 150px;margin: 0px;padding: 0px;"
                type="date"
                placeholder="选择日期">
              </el-date-picker>
              <span class="demonstration">到</span>
              <el-date-picker
                v-model="reportForm.endtime"
                value-format="yyyy-MM-dd HH:mm:ss"
                size="mini"
                style="width: 150px;margin: 0px;padding: 0px;"
                type="date"
                placeholder="选择日期">
              </el-date-picker>
              <el-select v-model="reportForm.missiontype" size="mini" style="width: 150px;margin: 0px;padding: 0px;" placeholder="请选择">
                <el-option
                  v-for="item in missiontype"
                  :key="item.value"
                  :label="item.value"
                  :value="item.id">
                </el-option>
              </el-select>
              <el-select v-model="reportForm.missionstatus"  size="mini" style="width: 150px;margin: 0px;padding: 0px;" placeholder="请选择">
                <el-option
                  v-for="item in missionstatus"
                  :key="item.value"
                  :label="item.label"
                  :value="item.value">
                </el-option>
              </el-select>
          </div>
        </div>
        <div style="margin-left: 5px;margin-right: 20px;display: inline">
          <el-button type="primary" size="mini" icon="el-icon-search"
                     @click="searchmission">
            查询
          </el-button>
          <el-button type="success" size="mini" @click="exportEmps"><i class="fa fa-lg fa-level-down"
                                                                       style="margin-right: 5px"></i>导出数据
          </el-button>
        </div>
      </el-header>
      <el-main style="padding-left: 0px;padding-top: 0px">
        <div>
          <transition name="slide-fade">
            <div
              style="margin-bottom: 10px;border: 1px;border-radius: 5px;border-style: solid;padding: 5px 0px 5px 0px;box-sizing:border-box;border-color: #20a0ff"
              v-show="advanceSearchViewVisible">
            </div>
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
              type="selection"
              align="left"
              width="30">
            </el-table-column>
            <el-table-column
              prop="id"
              align="left"
              fixed
              label="编号"
              width="50">
            </el-table-column>

            <el-table-column
              prop="username"
              align="left"
              fixed
              label="上报人"
              width="100">
            </el-table-column>
            <el-table-column
              prop="ptime"
              width="100"
              align="left"
              label="上报时间">
            </el-table-column>
            <el-table-column
              prop="typeName"
              label="任务类型"
              width="200">
            </el-table-column>
            <el-table-column
              prop="number"
              width="90"
              align="left"
              label="上报图片数">
              <template slot-scope="scope">
              <el-button type="text" style="padding: 3px 4px 3px 4px;margin: 2px" size="mini"
                           @click="showpicture(scope.row)">{{scope.row.number}}
              </el-button>
              </template>
            </el-table-column>
            <el-table-column
              prop="address"
              width="200"
              align="left"
              label="地址">
            </el-table-column>
            <el-table-column
              prop="mark"
              width="200"
              label="上报描述">
            </el-table-column>
            <el-table-column
              width="80"
              prop="status"
              :formatter="getstatus"
              label="审批状态">
            </el-table-column>
            <el-table-column
              width="80"
              prop="value"
              label="审批信息">
            </el-table-column>
            <el-table-column
              prop="score"
              width="80"
              label="得分">
            </el-table-column>
            <el-table-column
              fixed="right"
              label="操作"
              width="195">
              <template slot-scope="scope">
                <el-button type="danger" style="padding: 3px 4px 3px 4px;margin: 2px" size="mini"
                           @click="deleteEmp(scope.row)">删除
                </el-button>
              </template>
            </el-table-column> 
          </el-table>
          <div style="display: flex;justify-content: space-between;margin: 2px">
            <el-button type="danger" size="mini" v-if="emps.length>0" :disabled="multipleSelection.length==0"
                       @click="deleteManyEmps">批量删除
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
     <!-- <el-form :model="emp" :rules="rules" ref="addEmpForm" style="margin: 0px;padding: 0px;"> -->
    <div style="text-align: left">
        <el-dialog
          :title="dialogTitle"
          style="padding: 0px;"
          :close-on-click-modal="false"
          :visible.sync="dialogVisible"
          width="77%">
              <div style="display:inline;padding-right:10px"  v-for ="item in emp" :key="item.id">
              <img :src="item.address" width="100" height="100" alt="">
              </div>
          <span slot="footer" class="dialog-footer">
          <el-button size="mini" @click="cancelEidt">关 闭</el-button>
          </span>
        </el-dialog>
      </div>
     <!-- </el-form> -->
  </div>
</template>
<script>

export default {
  data() {
    return {
      emps: [],
      emp:[],
      keywords: "",
      fileUploadBtnText: "导入数据",
      ptime: "",
      faangledoubleup: "fa-angle-double-up",
      faangledoubledown: "fa-angle-double-down",
      dialogTitle: "",
      multipleSelection: [],
      depTextColor: "#c0c4cc",
      nations: [],
      politics: [],
      positions: [],
      joblevels: [],
      totalCount: -1,
      currentPage: 1,
      deps: [],
      defaultProps: {
        label: "name",
        isLeaf: "leaf",
        children: "children"
      },
      dialogVisible: false,
      tableLoading: false,
      advanceSearchViewVisible: false,
      showOrHidePop: false,
      showOrHidePop2: false,
      rules: {
      },
        missiontype:[],
        missionstatus:[{
          value: '',
          label: '全部'
        },{
          value: '0',
          label: '未审核'
        },{
          value: '1',
          label: '审核未通过'
        },{
          value: '2',
          label: '审核通过'
        }],
      reportForm: {
        starttime: "",
        endtime: "",
        missiontype: "",
        missionstatus: ""
      }
    };
    
  },
  mounted: function() {
    this.getmissiontype();
    this.loadEmps();
    console.log(this);
  },
  methods: {
  exportEmps(){
//        var iframe = document.createElement("iframe");
//        iframe.style.display = 'none';
//        iframe.src = "/employee/basic/exportEmp";
//        iframe.onload=function () {
//          document.body.removeChild(iframe);
//        }
//        document.body.appendChild(iframe);
        window.open("/employee/basic/exportEmp", "_parent");
      },
      getstatus(data){
      // console.log(data);
      if(data.status==0)
      {      
        return "未审核";
      }
      else if(data.status==1)
      {      
        return "审核未通过";
      }else if(data.status==2)
      {      
        return "审核通过";
      }
    },
    getmissiontype(){
      
        var _this =this;
        this.getRequest("/api/reportType/getmytaskmissiontype?userid=1").then(resp=>{
          //console.log("***************************************"+JSON.stringify(resp.data));
              var data = resp.data;
              if (resp && resp.status ==200){
                _this.missiontype = data.data.rows;
                console.log("*********");
              }
        })
    },
    searchmission() {
      this.loadEmps()
    },
    cancelSearch() {
      this.advanceSearchViewVisible = false;
      this.emptyEmpData();
      this.ptime = "";
      this.loadEmps();
    },
    
    handleSelectionChange(val) {
      this.multipleSelection = val;
    },
    deleteManyEmps() {
      this.$confirm(
        "此操作将删除[" + this.multipleSelection.length + "]条数据, 是否继续?",
        "提示",
        {
          confirmButtonText: "确定",
          cancelButtonText: "取消",
          type: "warning"
        }
      )
        .then(() => {
          var ids = "";
          for (var i = 0; i < this.multipleSelection.length; i++) {
            ids += this.multipleSelection[i].id + ",";
          }
          this.doDelete(ids);
        })
        .catch(() => {});
    },
    deleteEmp(row) {
      this.$confirm("此操作将永久删除[" + row.id+ "], 是否继续?", "提示", {
        confirmButtonText: "确定",
        cancelButtonText: "取消",
        type: "warning"
      })
        .then(() => {
          this.doDelete(row.id);
        })
        .catch(() => {});
    },
    doDelete(ids) {
      this.tableLoading = true;
      var _this = this;
      this.deleteRequest("/api/reportType/delmyreport/" + ids).then(resp => {
        _this.tableLoading = false;
        if (resp && resp.status == 200) {
          var data = resp.data;
          _this.$message({ type: data.status, message: data.msg });
          _this.loadEmps();
        }
      });
    },
    keywordsChange(val) {
      if (val == "") {
        this.loadEmps();
      }
    },
    searchEmp() {
      this.loadEmps();
    },
    currentChange(currentChange) {
      this.currentPage = currentChange;
      this.loadEmps();
    },
    loadEmps() {
      var _this = this;
      this.tableLoading = true;
      // console.log(this.$store.userid);
      this.getRequest(
        "api/reportType/mytask?page=" +
          this.currentPage +
          "&limit=10&userid=1&deptid=1&starttime="+this.reportForm.starttime+"&endtime="+this.reportForm.endtime+"&type="+this.reportForm.missiontype+"&status="+this.reportForm.missionstatus
          //  +this.$store.userid
      ).then(resp => {
        this.tableLoading = false;
        // console.log(resp.data.data.rows);
        if (resp && resp.data.status == 200) {
          var data = resp.data.data;
          _this.emps = data.rows;
          _this.totalCount = data.total;
        }
      });
    },
    showpicture(row) {
      // console.log(this.$store.userid);

      this.getRequest(
        "api/report/getPictureAddress?id=" + row.id
      ).then(resp => {
        this.emp=[];
        this.dialogVisible = true;
        // console.log(resp.data.data.rows);
        if (resp && resp.data.status == 200) {
          var data = resp.data.data;
          this.emp= data.rows;
        }
      });
    },
    cancelEidt() {
      this.dialogVisible = false;
      this.emptyEmpData();
    },
    showDepTree() {
      this.showOrHidePop = !this.showOrHidePop;
    },
    showDepTree2() {
      this.showOrHidePop2 = !this.showOrHidePop2;
    },
    handleNodeClick(data) {
      this.emp.departmentName = data.name;
      this.emp.departmentId = data.id;
      this.showOrHidePop = false;
      this.depTextColor = "#606266";
    },
    handleNodeClick2(data) {
      this.emp.departmentName = data.name;
      this.emp.departmentId = data.id;
      this.showOrHidePop2 = false;
      this.depTextColor = "#606266";
    },
    initData() {
      var _this = this;
      this.getRequest("/api/employee/basic/basicdata").then(resp => {
        if (resp && resp.status == 200) {
          var data = resp.data;
          _this.nations = data.nations;
          _this.politics = data.politics;
          _this.deps = data.deps;
          _this.positions = data.positions;
          _this.joblevels = data.joblevels;
          _this.emp.workID = data.workID;
        }
      });
    },
    emptyEmpData() {
      this.emp = {
        name: "",
        gender: "",
        birthday: "",
        idCard: "",
        wedlock: "",
        nationId: "",
        nativePlace: "",
        politicId: "",
        email: "",
        phone: "",
        address: "",
        departmentId: "",
        departmentName: "所属部门...",
        jobLevelId: "",
        posId: "",
        engageForm: "",
        tiptopDegree: "",
        specialty: "",
        school: "",
        beginDate: "",
        workState: "",
        workID: "",
        contractTerm: "",
        conversionTime: "",
        notWorkDate: "",
        beginContract: "",
        endContract: "",
        workAge: ""
      };
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
  transition: all 0.8s ease;
}

.slide-fade-leave-active {
  transition: all 0.8s cubic-bezier(1, 0.5, 0.8, 1);
}

.slide-fade-enter,
.slide-fade-leave-to {
  transform: translateX(10px);
  opacity: 0;
}
</style>
