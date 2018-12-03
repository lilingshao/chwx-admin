<template>
  <el-row style="margin-top:15px;">
    <el-col :span="9">
      <div>
        <div style="text-align: left">
          <el-input
            placeholder="输入语料集名称搜索..."
            style="width: 400px;margin: 0px;padding: 0px;"
            size="mini"
            prefix-icon="el-icon-search"
            v-model="keywords">
          </el-input>
        </div>
        <div>
          <el-tree
            :props="defaultProps"
            :data="treeData"
            ref="tree"
            :filter-node-method="filterNode"
            v-loading="treeLoading"
            default-expand-all
            highlight-current
            style="width: 400px;margin-top: 10px"
            @node-click="handleNodeClick"
            :expand-on-click-node="false"
            :render-content="renderContent">
          </el-tree>
          <div style="text-align: left">
            <el-dialog
              title="添加语料集"
              :visible.sync="dialogVisible"
              width="25%">
              <div>
                <span>上级语料集</span>
                <el-select v-model="pDep" style="width: 200px" placeholder="请选择" size="mini">
                  <el-option
                    v-for="item in allDeps"
                    :key="item.id"
                    :label="item.name"
                    :value="item.id">
                  </el-option>
                </el-select>
              </div>
              <div style="margin-top: 10px">
                <span>语料集名称</span>
                <el-input size="mini" style="width: 200px;" v-model="depName" placeholder="请输入语料集..." @keyup.enter.native="addDep"></el-input>
              </div>
              <span slot="footer" class="dialog-footer">
                <el-button size="small" @click="dialogVisible = false">取消</el-button>
                <el-button size="small" type="primary" @click="addDep">添加</el-button>
              </span>
            </el-dialog>
          </div>
          <div style="text-align: left">
            <el-dialog
              title="修改语料集"
              :visible.sync="updateVisible"
              width="25%">
              <div>
                <span>上级语料集</span>
                <el-select v-model="vCorpus" style="width: 200px" placeholder="请选择" size="mini">
                  <el-option
                    v-for="item in allDeps"
                    :key="item.id"
                    :label="item.name"
                    :value="item.id">
                  </el-option>
                </el-select>
              </div>
              <div style="margin-top: 10px">
                <span>语料集名称</span>
                <el-input size="mini" style="width: 200px;" v-model="corpusName" placeholder="请输入语料集..." @keyup.enter.native="updateCorpus"></el-input>
              </div>
              <span slot="footer" class="dialog-footer">
                <el-button size="small" @click="updateVisible = false">取消</el-button>
                <el-button size="small" type="primary" @click="updateCorpus">修改</el-button>
              </span>
            </el-dialog>
          </div>
        </div>
      </div>
    </el-col>
    <el-col :span="12" style="margin-top:-25px;">
      <h4>语料列表</h4>
      <div style="text-align: left">
        <el-input
          placeholder="输入语料搜索..."
          style="width: 220px;margin: -25px 0px 0px 0px;padding: 0px;"
          size="mini"
          prefix-icon="el-icon-search"
          :onkeydown="loadEmps"
          v-model="searchWords">
        </el-input>
        <el-button  style="font-size: 12px;padding:6px;" icon="el-icon-search" type="primary" size="mini" @click="loadEmps">搜索</el-button>
      </div>
      <div style="margin-top: 0px;">
        <el-container>
          <el-header style="padding: 0px;display:flex;justify-content:space-between;align-items: center;height:45px;">
            <div style="align-content: center;font-size: 12px" >
              <el-button  style="font-size: 12px;padding:6px;" icon="el-icon-plus" type="primary" size="mini" @click="addDataWin">添加</el-button>
              <el-button  style="font-size: 12px;padding:6px;" icon="el-icon-edit" type="primary" size="mini" @click="updateDataWin">修改</el-button>
              <el-button  style="font-size: 12px;padding:6px;" icon="el-icon-delete" type="primary" size="mini" @click="deleteData">删除</el-button>
            </div>
          </el-header>
        </el-container>
        <el-main style="padding-left: 0px;padding-top: 0px">
          <div>
            <transition name="slide-fade">
            </transition>
            <el-table
              :data="emps"
              border
              stripe
              @selection-change="handleSelectionChange"
              @select="changeFun"
              @select-all="changeFun"
              @row-click="clickRow"
              ref="tb"
              v-loading="tableLoading"
              size="mini"
              style="width: 120%">
              <el-table-column
                type="selection"
                prop='id'
                name="mybox"
                v-model='checkboxModel'
                width="35">
              </el-table-column><!--添加复选框列-->
              <el-table-column
                prop="index"
                align="center"
                fixed
                label="编号"
                width="50">
              </el-table-column>
              <el-table-column
                prop="content"
                align="left"
                fixed
                label="语料"
                >
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
      </div>
      <div style="text-align: left">
        <el-dialog
          title="添加语料"
          :visible.sync="addDataVisible"
          width="40%">
          <div style="margin-top: 5px">
            <el-input type="textarea" rows="5" v-model="corpusData" placeholder="请输入语料,每回车为一条语料..."></el-input>
          </div>
          <span slot="footer" class="dialog-footer">
            <el-button size="small" @click="addDataVisible = false">取消</el-button>
            <el-button size="small" type="primary" @click="addDataToStore">添加</el-button>
          </span>
        </el-dialog>
      </div>
      <div style="text-align: left">
        <el-dialog
          title="修改语料"
          :visible.sync="updateDataVisible"
          width="40%">
          <div style="margin-top: 5px">
            <el-input type="textarea" rows="3" v-model="updateCorpusData" ></el-input>
          </div>
          <span slot="footer" class="dialog-footer">
            <el-button size="small" @click="updateDataVisible = false">取消</el-button>
            <el-button size="small" type="primary" @click="updateDataToStore">修改</el-button>
          </span>
        </el-dialog>
      </div>
    </el-col>
  </el-row>
</template>
<script>
  export default {
    data() {
      return {
        id:'',
        keywords: '',
        depName: '',
        corpusName:'',
        treeLoading: false,
        dialogVisible: false,
        updateVisible: false,
        allDeps: [],
        pDep: '',
        vCorpus:'',
        treeData: [],
        totalCount:0,
        total:0,

        index:'',
        cid:'',
        content:'',
        emps: [],
        emp: {
          id:'',
          index:'',
          content:''
        },
        tableLoading: false,
        multipleSelection: [],
        currentPage: 1,
        removeArr:[],
        addDataVisible:false,
        corpusData:'',
        updateDataVisible:false,
        updateCorpusData:'',
        searchWords:'',

        defaultProps: {
          label: 'name',
          isLeaf: 'leaf',
          children: 'children'
        },
        checkboxModel:[],
        rules:{
          name:[
            {required:true,message:'请输入菜单名称',trigger:'blur'},
            {min:3,max:100,trigger:'blur'}
          ]
        }
      }
    },
    mounted: function () {
      this.treeLoading = true;
      this.loadTreeData();
      // this.loadAllDeps();
    },
    watch: {
      keywords(val) {
        this.$refs.tree.filter(val);
      },
      checkboxModel: {
        handler: function (val, oldVal) {
          console.log("-------------------")
          this.checked=true;
        },
        deep: true
      }
    },
    methods: {
      filterNode(value, data) {
        if (!value) return true;
        return data.name.indexOf(value) !== -1;
      },
      //点击树
      handleNodeClick(data) {
        console.log('click data:'+JSON.stringify(data));
        this.cid=data.id;
        this.vCorpus = data.fid;
        console.log('cid:'+data.id);
        this.currentPage=1;
        this.loadEmps();
        //清空选中的元素
        // this.removeArr = [];
       // event.stopPropagation()
     //   console.log('click data:'+JSON.stringify(data));
      },
      //修改语料集
      updateCorpus() {
        var _this = this;
        this.updateVisible = false;
        // this.treeLoading = true;

        var data={};
        data.name=this.corpusName;
        data.fid=this.vCorpus;
        data.id=this.id;
        console.log('update form:'+JSON.stringify(data));
        this.putRequestJson("/api/corpus/a"+this.id, data).then(resp=>{
          _this.treeLoading = false;
          if (resp && resp.status == 200) {
            _this.$message({type: resp.status, message: "更新成功!"});
            _this.loadTreeData();
          }
        });
        _this.loadAllDeps();
        _this.deleteLocalDep(_this.treeData, data);
        _this.setDataToTree(_this.treeData,data.parentId,data);

       // console.log('update data id:'+data.id);
       // console.log('update data:'+JSON.stringify(data));
      },
      loadTreeData(){
        // console.log("load...");
        var _this = this;
        this.getRequest("api/corpus/getCorpusByFid?fid=-1").then(resp=> {
         // console.log('resp loadTreeData：'+JSON.stringify(resp.data));
          _this.treeLoading = false;
          if (resp && resp.status == 200) {
            _this.treeData = resp.data;
          }
        })
      },
      setDataToTree(treeData,pId,respData){
       console.log('respD：'+respData);
       console.log('respData：'+JSON.stringify(respData));
       if(pId==0){
         // treeData[treeData.length]=respData;
       }
        for(var i=0;i<treeData.length;i++) {
          var td = treeData[i];
          if(td.id==pId) {
            treeData[i].children=treeData[i].children.concat(respData);
            return;
          }else{
            this.setDataToTree(td.children, pId, respData);
          }
        }
      },
      //添加语料集
      addDep(){
        var _this = this;
        this.dialogVisible = false;
        this.treeLoading = true;
        this.postRequestJson("/api/corpus/insertCorpus", {
          name: this.depName,
          fid: this.pDep
        }).then(resp=> {
          _this.treeLoading = false;
          if (resp && resp.status == 200) {
            console.log(JSON.stringify(resp));
            var respData = resp.data;
            _this.depName = '';
            _this.$message({type: respData.status, message: "添加成功!"});
            _this.setDataToTree(_this.treeData,_this.pDep,respData.data)
          }
        })
      },
      //加载所有的语料集
      loadAllDeps(){
        var _this = this;
        this.getRequest("/api/corpus/getAllByUid").then(resp=> {
          if (resp && resp.status == 200) {
          //  console.log('resp all：'+resp.data);
            _this.allDeps = resp.data;
          }
        });
      },
      showAddDepView(data, event){
        this.loadAllDeps();
        this.dialogVisible = true;
        this.pDep = data.id;
        event.stopPropagation()
      },
      showUpdateDepView(data, event){
        if(data.fid==-1){
          this.$message({
            message: '根节点不能修改!',
            type: 'warning'
          });
          return;
        }
        this.loadAllDeps();
        this.updateVisible = true;
        this.vCorpus = data.fid;
        this.id = data.id;
        this.corpusName = data.name;
        event.stopPropagation()
      },
      //删除语料集
      deleteDep(data, event){
        var _this = this;
        console.log(data.id);
        if(data.fid==-1){
          _this.$message({
            message: '根节点不能被删除!',
            type: 'warning'
          });
          return;
        }
        if (data.children!=null && data.children.length>0) {
          this.$message({
            message: '该语料集下尚有其他语料集，不能被删除!',
            type: 'warning'
          });
        } else {
          this.$confirm('删除[' + data.name + ']语料集, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
          }).then(() => {
            _this.treeLoading = true;
            _this.deleteRequest("/api/corpus/delCorpus/a" + data.id).then(resp=> {
              _this.treeLoading = false;
              if (resp && resp.status == 200) {
                var respData = resp.data;
                _this.$message({
                  message: "删除成功!",
                  type: respData.status
                });
                _this.deleteLocalDep(_this.treeData, data);
              }
            });
          }).catch(() => {
            _this.$message({
              type: 'info',
              message: '已取消删除'
            });
          });
        }
        event.stopPropagation()
      },
      deleteLocalDep(treeData,data){
        for(var i=0;i<treeData.length;i++) {
          var td = treeData[i];
          if(td.id==data.id) {
            treeData.splice(i, 1);
            return;
          }else{
            this.deleteLocalDep(td.children, data);
          }
        }
      },
      //选中表格行
      handleSelectionChange(val) {
        this.multipleSelection = val;
      },
      //加载语料
      loadEmps(){
        var _this = this;
        var params = "cid="+this.cid+"&page=" + this.currentPage + "&limit=10&word="+this.searchWords;
        this.getRequest("/api/corpus/getCorpusDataByCid?"+params).then(resp=> {
          if (resp && resp.status == 200) {
            var data = resp.data.data;
            _this.emps = data.rows;
            console.log("data.total=="+data.total);
            _this.totalCount = data.total;

          }
        })
      },
      currentChange(currentChange){
        console.log("currentChange=="+currentChange);
        this.currentPage = currentChange;
        this.loadEmps();
      },
      //复选框状态改变
      changeFun(val) {
        console.log("val=="+JSON.stringify(val));
        // this.removeArr = [];
        if(val){
          for(var i in val){
            this.removeArr[i]=(val[i].id);
          }
        }
        console.log("change=="+this.removeArr);
      },
      //删除语料
      deleteData(){
        if(this.removeArr.length<=0){
          this.$message({
            message: '选择要删除的数据!',
            type: 'warning'
          });
          return;
        }
        this.$confirm('删除[' + this.removeArr.length + ']条语料, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.treeLoading = true;
          this.deleteRequest("/api/corpus/delCorpusData/a" + this.removeArr.join()).then(resp=> {
            this.treeLoading = false;
            if (resp && resp.status == 200) {
              this.$message({
                message: "删除成功!"
              });
              this.removeArr = [];
              this.loadEmps();
            }
          });
        }).catch(() => {
          this.$message({
            type: 'info',
            message: '已取消删除'
          });
        });
        event.stopPropagation()
      },
      //添加语料window
      addDataWin(){
        console.log("addDataWin");
        this.corpusData='';
        if(this.vCorpus==''){
          this.$message({
            message: '请先选择语料集!',
            type: 'warning'
          });
          return;
        }
        if(this.vCorpus==-1){
          this.$message({
            message: '根语料集不能添加语料!',
            type: 'warning'
          });
          return;
        }
        this.addDataVisible=true;
      },
      //修改语料window
      updateDataWin(){
        console.log(this.removeArr.length);
        if(this.removeArr.length<=0){
          this.$message({
            message: '请选择要修改的数据!',
            type: 'warning'
          });
          return;
        }
        if(this.removeArr.length>1){
          this.$message({
            message: '修改的数据只能选择一条!',
            type: 'warning'
          });
          return;
        }
        this.updateCorpusData=this.multipleSelection[0].content;
        this.id=this.multipleSelection[0].id;
        this.updateDataVisible=true;
      },
      //添加语料
      addDataToStore(){
        var _this = this;
        console.log("data=="+_this.corpusData);
        if(this.strTrim(_this.corpusData)==''){
          this.$message({message:'请先输入语料!',type:'warning'});
          return;
        }

        this.addDataVisible = false;
        this.postRequestJson("/api/corpus/addCorpusData", {
          data: _this.corpusData,
          cid: _this.cid
        }).then(resp=> {
          _this.treeLoading = false;
          if (resp && resp.status == 200) {
            _this.$message({type: resp.status, message: "添加成功!"});
            this.loadEmps();
          }
        })
      },
      //修改语料
      updateDataToStore(){
        var _this = this;
        if(this.strTrim(_this.updateCorpusData)==''){
          this.$message({message:'请先输入语料!',type:'warning'});
          return;
        }

        this.updateDataVisible = false;
        this.putRequestJson("/api/corpus/updateCorpusData/a"+_this.id, {
          content: _this.updateCorpusData
        }).then(resp=> {
          _this.treeLoading = false;
          if (resp && resp.status == 200) {
            _this.$message({type: resp.status, message: "添加成功!"});
            this.removeArr = [];
            this.loadEmps();
          }
        })
      },
      //点击表格行
      clickRow(row){
        this.$refs.tb.toggleRowSelection(row);
        //判断id,存在则删除,不存在则添加
        {
          var have=-1;
          for(var i=0;i<this.removeArr.length;i++){
            if(this.removeArr[i]==row.id){
              have=i; break;
            }
          }
          if(have!=-1) this.removeArr.splice(have, 1);
          else this.removeArr.push(row.id);
        }
      },
      //去除空格
      strTrim(str){
        return str.replace(/(^\s*)|(\s*$)/g, "");
      },

      renderContent(h, {node, data, store}) {
        return (
          <span style="flex: 1; display: flex; align-items: center; justify-content: space-between; font-size: 14px; padding-right: 8px;">
          <span>
          <span>{node.label}</span>
        </span>
        <span>
        <el-button icon="el-icon-plus" style="font-size: 12px;" type="primary" size="mini" style="padding:3px" on-click={ () => this.showAddDepView(data,event) }>添加</el-button>
        <el-button icon="el-icon-edit" style="font-size: 12px;" type="primary" size="mini" style="padding:3px" on-click={ () => this.showUpdateDepView(data,event) }>修改</el-button>
        <el-button icon="el-icon-delete" style="font-size: 12px;" type="danger" size="mini" style="padding:3px" on-click={ () => this.deleteDep(data,event) }>删除</el-button>
        </span>
        </span>);
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
