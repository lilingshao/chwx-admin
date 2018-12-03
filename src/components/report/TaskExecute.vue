<template>
  <div>
    <el-container>
      <el-header style="padding: 0px;display:flex;justify-content:space-between;align-items: center">
        <div style="align-content: center;font-size: 14px"  >
            <el-col :span="5" >
              <span class="demonstration">从</span>
              <el-date-picker
                v-model="emp.ptime"
                value-format="yyyy-MM-dd HH:mm:ss"
                size="mini"
                type="date"
                placeholder="选择日期">
              </el-date-picker>
            </el-col>
          <el-col :span="5">
            <span class="demonstration">到</span>
            <el-date-picker
              v-model="emp.endtime"
              type="date"
              value-format="yyyy-MM-dd"
              size="mini"
              placeholder="结束日期">
            </el-date-picker>
          </el-col>
            <el-col :span="5"  >
              <treeselect  size="small" v-model="emp.deptids" :multiple="true" :options="depts" placeholder="请选择地区" :normalizer="normalizer"/>
            </el-col>
            <el-col :span="5" :push="1" >
              <el-select size="mini" v-model="emp.type" style="width: 200px" placeholder="请选择类型" prop="type">
                <el-option
                  v-for="item in type"
                  :key="item.id"
                  :label="item.value"
                  :value="item.id">
                </el-option>
              </el-select>
            </el-col>
            <el-col :span="3" :push="1" >
              <el-button icon="el-icon-search" type="primary" size="mini" @click="loadReports">搜索</el-button>
            </el-col>
        </div>

      </el-header>
      <div style=" text-align: left">
        <el-dialog title="任务审批" :visible.sync="dialogFormVisible" style="width: 100%;height: 100%">
          <el-form :model="auditForm" ref="auditForm" >
            <el-form-item label="分值:" :label-width="formLabelWidth" prop="score">
            <el-input v-model="auditForm.score" auto-complete="off" style="width: 50%" :readonly='true'></el-input>
            </el-form-item>
            <el-form-item label="审核与否:" :label-width="formLabelWidth">
              <el-select v-model="auditForm.status" prop="status" style="width: 320px" >
                <el-option
                  v-for="item in status"
                  :key="item.id"
                  :label="item.value"
                  :value="item.id">
                </el-option>
              </el-select>
            </el-form-item>
            <el-form-item label="审核信息:" :label-width="formLabelWidth">
              <el-input
                style="width: 320px;height: 300px"

                :rows="2"
                placeholder="请输入内容"
                v-model="auditForm.mark">
              </el-input>
            </el-form-item>
          </el-form>
          <div slot="footer" class="dialog-footer">
            <el-button @click="cancelAudit">取 消</el-button>
            <el-button type="primary" @click="submitForm(auditForm)">确 定</el-button>
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
              width="50">
            </el-table-column>

            <el-table-column
              prop="name"
              align="left"
              fixed
              label="上报人"
              width="80">
            </el-table-column>
            <el-table-column
              prop="ptime"
              width="180"
              align="left"
              label="上报时间">
            </el-table-column>

            <el-table-column
              prop="typeName"
              width="200"
              align="left"
              label="任务类型">
            </el-table-column>
            <el-table-column
              prop="number"
              width="100"
              align="left"
              label="上报图片数">
              <template slot-scope="scope">
                <el-button type="text" @click="showPictuer(scope.row)">
                  {{scope.row.number}}
                </el-button>
              </template>
            </el-table-column>
            <el-table-column
              prop="address"
              width="150"
              label="地址">
            </el-table-column>
            <el-table-column
              width="150"
              prop="markto"
              label="描述">
            </el-table-column>
            <el-table-column
              prop="status"
              width="180"
              label="审批状态">

              <template slot-scope="scope">
                <span v-if="scope.row.status=='0'" style="color: sandybrown">未审核</span>
                <span v-if="scope.row.status=='1'" style="color: red">审核未通过</span>
                <span v-if="scope.row.status=='2'" style="color: green">审核通过</span>
              </template>
            </el-table-column>
            <el-table-column
              prop="score"
              label="单条分值"
              width="110">
              <template slot-scope="scope">
                <span v-if="scope.row.status=='0'||scope.row.status=='1'" >0</span>
                <span v-if="scope.row.status=='2'">1</span>
              </template>
            </el-table-column>
            <el-table-column
              prop="operation"
              label="操作"
              width="110">
              <template slot-scope="scope">
                <el-button type="text" @click="audit(scope.row)" style="color: gold" >审核</el-button>
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
        dialogFormVisible: false,
        data: [],
        status: [{id:'2',value:'审核通过'},{id:'1',value:'审核未通过'}],
        type:[{id:'-1',value:'全部'},{id:'0',value:'未审核'},{id:'1',value:'审核未通过'},{id:'2',value:'审核通过'}],
        depts: [],
        emps: [],
        pictures:[],
        fileUploadBtnText: '导入数据',
        ptime: '',
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
          ptime: '2018-01-01',
          endtime: '2018-10-10',
          name: '',
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
      this.loadEmps();
    },
    methods: {
      cancelAudit(){
        this.dialogFormVisible = false;
        this.auditForm.mark = '';
        this.auditForm.status = '';
      },
      submitForm(formData){
        //添加
        this.tableLoading = true;
        var _this = this;
        //console.log('update form:'+JSON.stringify(formData));
        this.postRequestJson("api/reportTask/updateAudit", this.auditForm).then(resp=> {
         if (resp && resp.status == 200) {
            console.log(JSON.stringify(resp));
            _this.$message({type: resp.data.status, message: resp.data.msg});
            _this.loadEmps();
          }else{
           _this.$message({type: resp.data.status, message: resp.data.msg});
           _this.loadEmps();
         }
        })
        _this.dialogFormVisible = false;
       // _this.cancelAudit();
      },

      audit(row){
        console.log("*************"+JSON.stringify(row))
        //分数是根据图片数来决定的
        this.auditForm.score = row.number;
        this.auditForm.id = row.id;
        this.auditForm.type = row.status;
        this.cancelAudit();
        this.dialogFormVisible = true;

      },
      showPictuer(row){
        this.getRequest(
        "api/report/getPictureAddress?id=" + row.id
      ).then(resp => {
        this.pictures=[];
        this.dialogVisible = true;
        // console.log(resp.data.data.rows);
        if (resp && resp.data.status == 200) {
          var data = resp.data.data;
          this.pictures= data.rows;
        }
      });
      },
      formatterColum(row,column){
          switch (row.status){
            case 0:
              return '未审核';
              break;
            case 1:
              return '审核未通过';
              break;
            case 2:
              return '审核通过';
              break;
          }
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
      deleteEmp(row){
        this.$confirm('此操作将永久删除[' + row.name + '], 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.doDelete(row.id);
        }).catch(() => {
        });
      },
      doDelete(ids){
        this.tableLoading = true;
        var _this = this;
        this.deleteRequest("/report/delete/" + ids).then(resp=> {
          _this.tableLoading = false;
          if (resp && resp.status == 200) {
            var data = resp.data;
            _this.$message({type: data.status, message: data.msg});
            _this.loadEmps();
          }
        })
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
    loadReports(){
       var _this = this;
        this.tableLoading = true;
        //console.log("***********************"+this.emp.status);
            this.getRequest("api/reportTask/reportAudit?page="+this.currentPage+"&limit=10"+"&ptime="+this.emp.ptime+"&endtime="+this.emp.endtime+"&deptid="+this.emp.deptids+"&status="+this.emp.type).then(resp=> {
          this.tableLoading = false;
          if (resp && resp.status == 200) {
            var data = resp.data.data;
            _this.emps = data.rows;
            _this.totalCount = data.total;
//            _this.emptyEmpData();
          }
        })
      },
      loadEmps(){
        var _this = this;
        this.tableLoading = true;
        this.getRequest("api/reportTask/reportAudit?page="+this.currentPage+"&limit=10"+"&ptime="+this.emp.ptime+"&endtime="+this.emp.endtime+"&deptid="+this.emp.deptids+"&status="+this.emp.type).then(resp=> {
          this.tableLoading = false;
          //console.log("***********"+JSON.stringify(resp));
          if (resp && resp.data.status == 200) {
            var data = resp.data.data;
            _this.emps = data.rows;
            _this.totalCount = data.total;
          }
        })
      },
      addEmp(formName){
        var _this = this;
        this.$refs[formName].validate((valid) => {
          if (valid) {
            if (this.emp.id) {
              //更新
              this.tableLoading = true;
              this.putRequest("/employee/basic/emp", this.emp).then(resp=> {
                _this.tableLoading = false;
                if (resp && resp.status == 200) {
                  var data = resp.data;
                  _this.$message({type: data.status, message: data.msg});
                  _this.dialogVisible = false;
                  _this.emptyEmpData();
                  _this.loadEmps();
                }
              })
            } else {
              //添加
              this.tableLoading = true;
              this.postRequest("/employee/basic/emp", this.emp).then(resp=> {
                _this.tableLoading = false;
                if (resp && resp.status == 200) {
                  var data = resp.data;
                  _this.$message({type: data.status, message: data.msg});
                  _this.dialogVisible = false;
                  _this.emptyEmpData();
                  _this.loadEmps();
                }
              })
            }
          } else {
            return false;
          }
        });
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
            console.log(_this.depts);
            _this.data = data.data;
          }
        })
      },
      emptyEmpData(){
        this.emp = {
          name: '',
          address: '',
          deptids: null,
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
