<template>
  <el-row style="margin-top: 15px">
    <el-col :span="12">
    </el-col>
    <el-col :span="12">
      <h3>发布任务</h3>
      <el-form :model="reportForm" :rules="rules" ref="reportForm" label-width="100px">
        <el-form-item label="任务名称：" prop="name">
          <el-input v-model="reportForm.name"></el-input>
        </el-form-item>

          <el-form-item label="任务类型：" prop="typename">
          <el-select v-model="reportForm.type" style="width: 200px" placeholder="请选择平台" prop="type">
            <el-option
              v-for="item in type"
              :key="item.id"
              :label="item.value"
              :value="item.id">
            </el-option>
          </el-select>

          <el-select v-model="reportForm.typeAfter" style="width: 200px" placeholder="请选择类型" prop="typeAfter" >
            <el-option
              v-for="item in typeAfter"
              :key="item.id"
              :label="item.value"
              :value="item.value">
            </el-option>
          </el-select>
        </el-form-item>

        <el-form-item label="任务链接：" prop="address">
          <el-input v-model="reportForm.address"></el-input>
        </el-form-item>

        <el-form-item label="下发对象：" prop="deptids" width="400">

        <!--  <el-input  v-model="reportForm.departmentName" number></el-input>


            <el-popover
              placement="right"
              width="400"
              trigger="click"
              >
              <el-tree :data="depts" :default-expand-all="false" :props="defaultProps" :expand-on-click-node="false"
                       @node-click="handleNodeClick" show-checkbox ></el-tree>
              <el-button size="mini" type="text" round @click="">取消</el-button>
              <el-button size="mini" type="text" round>确定</el-button>
              <el-button slot="reference">选择</el-button>

            </el-popover>-->


          <treeselect v-model="reportForm.deptids" :multiple="true" :options="depts" placeholder="请选择部门" :normalizer="normalizer"/>
        </el-form-item>

        <el-form-item label="执行条数：" prop="number">
          <el-input v-model="reportForm.number" number></el-input>
        </el-form-item>
        <el-form-item label="每条分值：" prop="score">
          <el-select v-model="reportForm.score" style="width: 200px" placeholder="请选择类型">
            <el-option
              v-for="item in score"
              :key="item.id"
              :label="item.value"
              :value="item.id">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="任务描述：" prop="mark">
          <el-input v-model="reportForm.mark"></el-input>
        </el-form-item>

        <el-form-item label="截止时间：" prop="endtime" >
          <el-date-picker
            v-model="reportForm.endtime"
            value-format="yyyy-MM-dd HH:mm:ss"
            style="width: 200px"
            type="datetime"
            placeholder="请选择截止时间">
          </el-date-picker>
        </el-form-item>

        <el-form-item label="通知方式：" prop="notice">
          <el-radio-group v-model="reportForm.notice">
            <el-radio style="margin-left: 15px" label="1">短信通知</el-radio>
            <el-radio style="margin-left: 15px" label="2">微信通知</el-radio>
          </el-radio-group>
        </el-form-item>

        <el-form-item>
          <el-button type="primary" @click="onSubmit(reportForm)">发布</el-button>
          <el-button @click="cancleEditForm">取消</el-button>
        </el-form-item>
      </el-form>
    </el-col>
  </el-row>
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
        deptids: null,
        keywords: '',
        menuName: '',
        treeLoading: false,
        dialogVisible: false,
        showOrHidePop: false,
        depTextColor: '#c0c4cc',
        allMenus: [],
        depts: [],
        type: [],
        typeAfter:[{id:'1',value:'直发'},{id:'2',value:'转发'},{id:'3',value:'点赞'},{id:'4',value:'评论'}],
        score:[{id:'1',value:'1'},{id:'2',value:'2'},{id:'3',value:'3'},{id:'4',value:'4'},{id:'5',value:'5'}],
        defaultProps: {
          label: 'name',
          isLeaf: 'leaf',
          children: 'children'
        },
        reportForm:{
          typeName: '',
          name: '',
          type: [] ,
          typeAfter: [],
          address: '',
          deptids: null,
          number: '',
          score: [],
          endtime: '',
          mark: '',
          notice: ''
        },
        rules:{
          name:[
            {required:true,message:'请输入任务名称',trigger:'blur'},
            {min:3,max:100,trigger:'blur'}
          ]
        }
      }
    },
    mounted: function () {
      this.initData();
      this.treeLoading = true;
      this.loadAllDeps();
    },
    watch: {
      keywords(val) {
        this.$refs.tree.filter(val);
      }
    },
    methods: {
      filterNode(value, data) {
        if (!value) return true;
        return data.name.indexOf(value) !== -1;
      },
      handleNodeClick(data) {
        console.log('click data:'+JSON.stringify(data));
        this.reportForm.departmentName += data.name+",";
        this.reportForm.deptids += data.id;
        this.showOrHidePop = false;
        this.depTextColor = '#606266';
      },
      initData(){
        var _this =this;
        this.getRequest("/api/ndept/deptTree/1").then(resp=>{
          //console.log("***************************************"+JSON.stringify(resp.data));
              var data = resp.data;
              if (resp && resp.status ==200){
                _this.depts = data.data;
                //console.log("*********"+JSON.stringify(_this.depts));
              }
        })
      },
      showDepTree(){
        this.showOrHidePop = !this.showOrHidePop;
      },
      onSubmit(formName) {
        //添加
        this.tableLoading = true;
        var _this = this;
        console.log('form ****** :'+JSON.stringify(formName));
        this.postRequestJson("api/report/publishReport", this.reportForm).then(resp=> {
          if (resp && resp.status == 200) {
            console.log("ssssssssssssssss"+JSON.stringify(resp));
            _this.$message({type: resp.status, message: resp.msg});
            _this.emptyFormData();
            _this.loadEmps();
          }
        })
      },
      loadEmps(){
        var _this = this;
        this.tableLoading = true;
        this.getRequest("api/report/page?page=" + this.currentPage + "&limit=10&name=" + this.keywords ).then(resp=> {
          this.tableLoading = false;
          //console.log("***********"+JSON.stringify(resp));
          if (resp && resp.data.status == 200) {
            var data = resp.data.data;
            _this.emps = data.rows;
            _this.totalCount = data.total;
          }
        })
      },
      cancleEdit(){

      },

      cancleEditForm(){
        this.emptyFormData();
      },
      emptyFormData(){
        this.reportForm={
          typeName: '',
          name: '',
          type: [] ,
          typeAfter: [],
          address: '',
          deptids: null,
          number: '',
          score: [],
          endtime: '',
          mark: '',
          notice: ''
        }
      },
      loadAllDeps(){
        var _this = this;
        this.getRequest("/api/reportType/getType").then(resp=> {
          //console.log("***********"+JSON.stringify(resp))
          if (resp && resp.status == 200) {
            _this.type = resp.data.data.rows;
          }
        });
      },
    }
  };
</script>
<style lang="stylus" rel="stylesheet/stylus">
  //@import './DepMana.styl';
</style>
