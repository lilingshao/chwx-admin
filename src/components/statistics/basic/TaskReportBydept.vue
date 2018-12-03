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
                 v-model="reportForm.starttime"
                 value-format="yyyy-MM-dd"
                 size="mini"
                 type="date"
                 style="width: 200px"
                 placeholder="选择日期">
               </el-date-picker>
               <span class="demonstration">到</span>
               <el-date-picker
                 v-model="reportForm.endtime"
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
             <treeselect style="width: 200px;" v-model="reportForm.deptids" :multiple="true" :options="depts" placeholder="请选择地区" :normalizer="normalizer"/>
             <el-button icon="el-icon-search" type="primary" size="mini" @click="searchmission">搜索</el-button>

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
            :data="data"
            v-loading="tableLoading"
            border
            stripe
            size="mini"
            style="width: 100%">
            <el-table-column
              prop="id"
              align="left"
              fixed
              label="编号"
              width="200">
            </el-table-column>
            <el-table-column
              prop="name"
              width="400"
              align="left"
              label="部门">
            </el-table-column>
            <el-table-column
              prop="number"
              label="执行条数"
              width="300">
            </el-table-column>
            <el-table-column
              prop="score"
              width="300"
              align="left"
              label="得分">
            </el-table-column>
          </el-table>
          <div style="display: flex;justify-content: space-between;margin: 2px">
            <el-button type="text" size="mini" disabled>
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
      data:[],
      depts:[],
      faangledoubleup: "fa-angle-double-up",
      faangledoubledown: "fa-angle-double-down",
      totalCount: -1,
      currentPage: 1,
      dialogVisible: false,
      tableLoading: false,
      advanceSearchViewVisible: false,
      showOrHidePop: false,
      showOrHidePop2: false,
        missiontype:[],
      reportForm: {
        starttime: "",
        endtime: "",
        deptids:null
      }
    };

  },
  mounted: function() {
    this.getdepttree();
    this.initDate();
    this.loadEmps();
    console.log(this);
  },
  methods: {
    initDate(){
        let nowDate = new Date();
        let year = nowDate.getFullYear();
        let month = nowDate.getMonth() + 1;
        let day = nowDate.getDate();
        let endTime = `${year}-${month}-${day}`;
        this.reportForm.endtime = endTime;//当前时间点
        this.reportForm.starttime = `${year}-${month}-1`;
        this.month.id = month;
        console.log("***   "+this.month.id);
      },
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
        this.reportForm.endtime = endTime;//当前时间点
        this.reportForm.starttime = `${year}-${month}-1`;
        this.loadEmps();
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
        this.reportForm.starttime = `${year}-${lastMonth}-01`;
        this.reportForm.endtime =`${year}-${lastMonth}-${flag.getDate()}`;
        this.loadEmps();
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
        this.reportForm.starttime = `${year}-${lastMonth}-01`;
        this.reportForm.endtime =`${year}-${lastMonth}-${flag.getDate()}`;
        this.loadEmps();
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
        this.reportForm.starttime = `${year}-${lastMonth}-01`;
        this.reportForm.endtime =`${year}-${lastMonth}-${flag.getDate()}`;
        this.loadEmps();
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
    getdepttree(){
      var _this =this;
      this.getRequest("/api/ndept/deptTree/1").then(resp=>{
        //console.log("***************************************"+JSON.stringify(resp.data));
            var data = resp.data;
            if (resp && resp.status ==200){
              _this.depts = data.data;
              console.log("*********"+JSON.stringify(_this.depts));
            }
      })
    },
    searchmission() {
      this.loadEmps()
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
        "api/reportTask/byDeptReport?page=" +
          this.currentPage +
          "&limit=10&userid=1&deptid="+this.reportForm.deptids+"&starttime="+this.reportForm.starttime+"&endtime="+this.reportForm.endtime
          //  +this.$store.userid
      ).then(resp => {
        this.tableLoading = false;
        // console.log(resp.data.data.rows);
        if (resp && resp.data.status == 200) {
          var data = resp.data.data;
          _this.data = data.rows;
          _this.totalCount = data.total;
        }
      });
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

  .vue-treeselect__control {
    padding-left: 5px;
    padding-right: 5px;
    display: table;
    table-layout: fixed;
    width: 100%;
    height: 28px;

  }

</style>
