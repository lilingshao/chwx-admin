<template>
  <el-row>
    <el-col :span="12">
      <div>
        <div style="text-align: left">
          <el-input
            placeholder="输入部门名称搜索部门..."
            style="width: 500px;margin: 0px;padding: 0px;"
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
            show-checkbox
            highlight-current
            style="width: 500px;margin-top: 10px"
            @node-click="handleNodeClick"
            :expand-on-click-node="false"
            :render-content="renderContent">
          </el-tree>
          <div style="text-align: left">
            <el-dialog
              title="添加部门"
              :visible.sync="dialogVisible"
              width="25%">
              <div>
                <span>上级部门</span>
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
                <span>部门名称</span>
                <el-input size="mini" style="width: 200px;" v-model="depName" placeholder="请输入部门名称..." @keyup.enter.native="addDep"></el-input>
              </div>
              <span slot="footer" class="dialog-footer">
                <el-button size="small" @click="dialogVisible = false">取消</el-button>
                <el-button size="small" type="primary" @click="addDep">添加</el-button>
              </span>
            </el-dialog>
          </div>
        </div>
      </div>
    </el-col>
    <el-col :span="12">
      <h3>修改部门</h3>
      <el-form :model="deptForm" :rules="rules" ref="deptForm" label-width="100px">
        <el-form-item label="单位名称：" prop="name">
          <el-input v-model="deptForm.name"></el-input>
        </el-form-item>
        <el-form-item label="上级部门：" prop="parentId">
        <el-select v-model="deptForm.upDep" style="width: 200px" placeholder="请选择" >
          <el-option
            v-for="item in allDeps"
            :key="item.id"
            :label="item.name"
            :value="item.id">
          </el-option>
        </el-select>
        </el-form-item>
        <el-form-item label="是否启用：" prop="enabled">
          <el-switch v-model="deptForm.enabled"></el-switch>
        </el-form-item>
        <el-form-item>
          <el-input v-model="deptForm.parentId" type="hidden"></el-input>
          <el-button type="primary" @click="onSubmit(deptForm)">更新</el-button>
          <el-button>取消</el-button>
        </el-form-item>
      </el-form>
    </el-col>
  </el-row>
</template>
<script>
  export default {
    data() {
      return {
        keywords: '',
        depName: '',
        treeLoading: false,
        dialogVisible: false,
        allDeps: [],
        pDep: '',
        treeData: [],
        defaultProps: {
          label: 'name',
          isLeaf: 'leaf',
          children: 'children'
        },
        deptForm:{
          id: '',
          name: '',
          parentId: '',
          enabled: '',
          upDep: []
        },
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
       // this.deptForm=data;
        this.deptForm.id=data.id;
        this.deptForm.name=data.name;
        this.deptForm.parentId=data.parentId;
        this.deptForm.enabled=data.enabled;
        this.deptForm.upDep = data.parentId;
       // event.stopPropagation()
     //   console.log('click data:'+JSON.stringify(data));
      },
      onSubmit(data) {
        var _this = this;
        data.parentId=data.upDep;
        console.log('update form:'+JSON.stringify(data));
        this.putRequestJson("/api/dept/a"+data.id, data).then(resp=>{
          _this.treeLoading = false;
          if (resp && resp.status == 200) {
            _this.$message({type: resp.status, message: "更新成功!"});
            _this.loadTreeData();
          }
        });
        //_this.loadAllDeps();
        _this.deleteLocalDep(_this.treeData, data);
        _this.setDataToTree(_this.treeData,data.parentId,data);

       // console.log('update data id:'+data.id);
       // console.log('update data:'+JSON.stringify(data));
      },
      loadTreeData(){
        var _this = this;
        this.getRequest("api/system/basic/dep/-1").then(resp=> {
            //this.getRequest("api/ndept/deptTree/1").then(resp=> {
         // console.log('resp loadTreeData：'+JSON.stringify(resp.data));
          _this.treeLoading = false;
          if (resp && resp.status == 200) {
            _this.treeData = resp.data;
          }
        })
      },
      setDataToTree(treeData,pId,respData){
       // console.log('respData：'+JSON.stringify(respData));
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
      addDep(){
        var _this = this;
        this.dialogVisible = false;
        this.treeLoading = true;
        this.postRequest("/api/system/basic/dep", {
          name: this.depName,
          parentId: this.pDep
        }).then(resp=> {
          _this.treeLoading = false;
          if (resp && resp.status == 200) {
            var respData = resp.data;
            _this.depName = '';
            _this.$message({type: respData.status, message: "添加成功!"});
            _this.setDataToTree(_this.treeData,_this.pDep,respData.msg)
          }
        })
      },
      loadAllDeps(){
        var _this = this;
        this.getRequest("/api/system/basic/deps").then(resp=> {
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
      deleteDep(data, event){
        var _this = this;
        if (data.children.length>0) {
          this.$message({
            message: '该部门下尚有其他部门，不能被删除!',
            type: 'warning'
          });
        } else {
          this.$confirm('删除[' + data.name + ']部门, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
          }).then(() => {
            _this.treeLoading = true;
            _this.deleteRequest("/api/system/basic/dep/" + data.id).then(resp=> {
              _this.treeLoading = false;
              if (resp && resp.status == 200) {
                var respData = resp.data;
                _this.$message({
                  message: respData.msg,
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
      renderContent(h, {node, data, store}) {
        return (
          <span style="flex: 1; display: flex; align-items: center; justify-content: space-between; font-size: 14px; padding-right: 8px;">
          <span>
          <span>{node.label}</span>
        </span>
        <span>
        <el-button style="font-size: 12px;" type="primary" size="mini" style="padding:3px" on-click={ () => this.showAddDepView(data,event) }>添加部门</el-button>
        <el-button style="font-size: 12px;" type="danger" size="mini" style="padding:3px" on-click={ () => this.deleteDep(data,event) }>删除部门</el-button>
        </span>
        </span>);
      }
    }
  };
</script>
<style lang="stylus" rel="stylesheet/stylus">
  @import './DepMana.styl';
</style>
