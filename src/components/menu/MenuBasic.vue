<template>
  <el-row style="margin-top: 15px">
    <el-col :span="12">
      <div>
        <div style="text-align: left">
          <el-input
            placeholder="输入部门名称搜索菜单..."
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
            highlight-current
            style="width: 500px;margin-top: 10px"
            @node-click="handleNodeClick"
            :expand-on-click-node="false"
            :render-content="renderContent">
          </el-tree>
          <div style="text-align: left">
            <el-dialog
              title="添加菜单"
              :visible.sync="dialogVisible"
              width="25%">
              <div>
                <span>上级菜单</span>
                <el-select v-model="pDep" style="width: 200px" placeholder="请选择" size="mini">
                  <el-option
                    v-for="item in allMenus"
                    :key="item.id"
                    :label="item.name"
                    :value="item.id">
                  </el-option>
                </el-select>
              </div>
              <div style="margin-top: 10px">
                <span>菜单名称</span>
                <el-input size="mini" style="width: 200px;" v-model="menuName" placeholder="请输入菜单名称..." @keyup.enter.native="addDep"></el-input>
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
      <h3>修改菜单</h3>
      <el-form :model="menuForm" :rules="rules" ref="menuForm" label-width="100px">
        <el-form-item label="菜单名称：" prop="name">
          <el-input v-model="menuForm.name"></el-input>
        </el-form-item>
        <el-form-item label="父菜单：" prop="parentId">
          <el-select v-model="menuForm.upDep" style="width: 200px" placeholder="请选择" >
            <el-option
              v-for="item in allMenus"
              :key="item.id"
              :label="item.name"
              :value="item.id">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="路径：" prop="path">
          <el-input v-model="menuForm.path"></el-input>
        </el-form-item>
        <el-form-item label="url：" prop="url">
          <el-input v-model="menuForm.url"></el-input>
        </el-form-item>
        <el-form-item label="组件名称：" prop="component">
          <el-input v-model="menuForm.component"></el-input>
        </el-form-item>
        <el-form-item label="图标：" prop="iconCls">
          <el-input v-model="menuForm.iconCls"></el-input>
        </el-form-item>
        <el-form-item label="是否启用：" prop="enabled">
          <el-switch v-model="menuForm.enabled"></el-switch>
        </el-form-item>
        <el-form-item>
          <el-button type="primary" @click="onSubmit">更新</el-button>
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
        menuName: '',
        treeLoading: false,
        dialogVisible: false,
        allMenus: [],
        pDep: '',
        treeData: [],
        defaultProps: {
          label: 'name',
          isLeaf: 'leaf',
          children: 'children'
        },
        menuForm:{
          name: '',
          enabled: false,
          type: [],
          path: '',
          url: '',
          component: '',
          iconCls: '',
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
        this.menuForm.id=data.id;
        this.menuForm.name=data.name;
        this.menuForm.path=data.path;
        this.menuForm.url=data.url;
        this.menuForm.component=data.component;
        this.menuForm.iconCls=data.iconCls;
        this.menuForm.parentId=data.parentId;
        this.menuForm.enabled=data.enabled;
        this.menuForm.upDep = data.parentId;
      },
      onSubmit() {
        console.log('submit!');
      },
      loadTreeData(){
        var _this = this;
        //this.getRequest("/system/basic/menuTree/-1").then(resp=> {
        this.getRequest("/api/menu/menuTree/-1").then(resp=> {
          console.log('resp loadTreeData：'+JSON.stringify(resp.data));
          _this.treeLoading = false;
          if (resp && resp.status == 200) {
            _this.treeData = resp.data;
          }
        })
      },
      setDataToTree(treeData,pId,respData){
        console.log('respData：'+JSON.stringify(respData));
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
        this.postRequestJson("/api/system/basic/dep", {
          name: this.menuName,
          parentId: this.pDep
        }).then(resp=> {
          _this.treeLoading = false;
          if (resp && resp.status == 200) {
            var respData = resp.data;
            _this.menuName = '';
            _this.$message({type: respData.status, message: "添加成功!"});
            _this.setDataToTree(_this.treeData,_this.pDep,respData.msg)
          }
        })
      },
      loadAllDeps(){
        var _this = this;
        this.getRequest("/api/menu/all").then(resp=> {
          if (resp && resp.status == 200) {
            console.log('resp all：'+resp.data);
            _this.allMenus = resp.data;
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
            message: '该菜单下尚有其他子菜单，不能被删除!',
            type: 'warning'
          });
        } else {
          this.$confirm('删除[' + data.name + ']菜单, 是否继续?', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
          }).then(() => {
            _this.treeLoading = true;
            _this.deleteRequest("/system/basic/dep/" + data.id).then(resp=> {
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
        <el-button style="font-size: 12px;" type="primary" size="mini" style="padding:3px" on-click={ () => this.showAddDepView(data,event) }>添加菜单</el-button>
        <el-button style="font-size: 12px;" type="danger" size="mini" style="padding:3px" on-click={ () => this.deleteDep(data,event) }>删除菜单</el-button>
        </span>
        </span>);
      }
    }
  };
</script>
<style lang="stylus" rel="stylesheet/stylus">
  //@import './DepMana.styl';
</style>
