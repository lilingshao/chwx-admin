<template>
  <el-row style="margin-top: 15px">
     
    <el-col :span="4">
      <div>
        <h3>部门结构</h3>
        <div>
          <el-tree
            :props="defaultProps"
            :data="departments"
            ref="tree"
            :filter-node-method="filterNode"
            v-loading="treeLoading"
            default-expand-all
            highlight-current
            style="width: 500px;margin-top: 10px"
            @node-click="NodeClick1"
            :expand-on-click-node="false"
            >
          </el-tree>
          
        </div>
      </div>
    </el-col>
    <el-col :span="12">
      <h3>部门的用户列表</h3>
      
         
        
      <el-button type="primary" size="mini" v-if="accountlist.length>0" :disabled="multipleSelection.length==0"
                       @click="changeUserRole">保存用户角色关系
      </el-button>
      <treeselect  size="mini" v-model="deptidselect" :multiple="false" :options="departments2" placeholder="请选择部分" :normalizer="normalizer"/>
      <el-table
            :data="accountlist"
            v-loading="tableLoading"
            border
            stripe
            @selection-change="handleSelectionChange"
            highlight-current-row
            @current-change="handleCurrentChange"
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
              width="70">
            </el-table-column>
            <el-table-column
              prop="name"
              align="left"
              fixed
              label="用户名"
              width="70">
            </el-table-column>
            <el-table-column
              prop="id"
              align="tel"
              fixed
              label="手机号码"
              width="70">
            </el-table-column>
            <el-table-column
              prop="roleid"
              width="100"
              align="left"
              label="角色"
              :formatter="getrolebyroleid"
              >
            </el-table-column>
          </el-table>
          <div style="display: flex;justify-content: space-between;margin: 2px">
            <el-pagination
              background
              :page-size="10"
              :current-page="currentPage"
              @current-change="currentChange"
              layout="prev, pager, next"
              :total="totalCount">
            </el-pagination>
          </div>
    </el-col>
    <el-col :span="4">
      <div>
        <h3>角色</h3>
            <el-button type="primary" size="mini" v-if="menulist.length>0"
                       @click="changeRolemenu">保存角色权限关系
            </el-button>
            <el-radio v-for="item in rolelist"
              style="display:block;margin: 2px"
              v-model="checkedroleid"
              @change="changecheckedrole"
              :key="item.id"
              :label="item.id"
              :value="item.id">
              {{item.value}}
              </el-radio>
            
      </div>
    </el-col>
    <el-col :span="4">
      <div>
        <h3>权限功能</h3>
        <div>
          <el-tree
            :props="defaultProps"
            :data="menulist"
            ref="tree"
            :filter-node-method="filterNode"
            v-loading="treeLoading"
            default-expand-all
            highlight-current
            style="width: 500px;margin-top: 10px"
            @node-click="NodeClick2"
            show-checkbox
            node-key="id"
            >
          </el-tree>
        </div>
      </div>
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
        keywords: '',
        menuName: '',
        treeLoading: false,
        dialogVisible: false,
        allMenus: [],
        checkedmenu:[],
        pDep: '',
        treeData: [],
        accountlist:[],
        deptidselect:'',
        checkedroleid:'',
        test:null,
        rolelist:[],
        menulist:[],
        rolemenulist:[],
        departments:[],
        departments2:[],
        tableLoading: false,
        currentRow:null,
        defaultProps: {
          label: 'name',
          isLeaf: 'leaf',
          children: 'children'
        },
        currentPage:1,
        totalCount:0,
        multipleSelection: [],
        putform1:{
          ids:'',
          checkedroleid:''
        },
        putform2:{
          ids:'',
          checkedroleid:''
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
      this.tableLoading = true;
      this.treeLoading = true;
      this.loadDepartments();
      this.loadRoles();
      this.loadMenulist();
      this.loadUsers();
      this.loadRoleMenu();
      // this.loadTreeData();
      // this.loadAllDeps();
    },
    watch: {
      keywords(val) {
        this.$refs.tree.filter(val);
      }
    },
    methods: {
      changecheckedrole()
      {
        this.changecheckedmenu();
      },
      changecheckedmenu() {
        // console.log("start");
        this.checkedmenu=[];
        for(var i=0;i<this.rolemenulist.length;i++)
        {
          if(this.rolemenulist[i].roleid==this.checkedroleid)
          {
            this.checkedmenu.push(this.rolemenulist[i].rightid);
          }
        }
         this.$refs.tree.setCheckedKeys(this.checkedmenu);
      },
      handleCurrentChange(val) {
        this.currentRow = val;
        this.checkedroleid =val.roleid;
        this.changecheckedmenu();
        // console.log(this.currentRow);
      },
      currentChange(currentChange) {
      this.currentPage = currentChange;
      this.loadUsers();
      },
      changeRolemenu()
      {
        var _this = this;
          this.$confirm('此操作将修改角色权限关系, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          var ids = '';
          var data=_this.$refs.tree.getCheckedKeys();
          for (var i = 0; i < data.length; i++) {
            ids += data[i] + ",";
          }
          this.changerolemenu(ids);
        }).catch(() => {
        });
      },
      changerolemenu(ids) {
      this.tableLoading = true;
      var _this = this;
      this.putform2.ids=ids;
      this.putform2.checkedroleid=this.checkedroleid;
      this.putRequest("/api/system/user/updaterolemenu",this.putform2).then(resp => {
        _this.tableLoading = false;
        if (resp && resp.status == 200) {
          var data = resp.data;
          _this.$message({ type: data.status, message: data.msg });
          _this.loadRoleMenu();
        }
      });
      },
      changeUserRole(){
        this.$confirm('此操作将修改[' + this.multipleSelection.length + ']位用户的角色权限, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          var ids = '';
          for (var i = 0; i < this.multipleSelection.length; i++) {
            ids += this.multipleSelection[i].id + ",";
          }
          this.changerolebyid(ids);
        }).catch(() => {
        });
      },
      changerolebyid(ids) {
      this.tableLoading = true;
      var _this = this;
      this.putform1.ids=ids;
      this.putform1.checkedroleid=this.checkedroleid;
      this.putRequest("/api/system/user/changerolebyid",this.putform1).then(resp => {
        _this.tableLoading = false;
        if (resp && resp.status == 200) {
          var data = resp.data;
          _this.$message({ type: data.status, message: data.msg });
          _this.loadUsers();
        }
      });
      },
      filterNode(value, data) {
        if (!value) return true;
        return data.name.indexOf(value) !== -1;
      },
      getrolebyroleid(data){
      // console.log(data);
      for(var i=0;i<this.rolelist.length;i++)
        {
          if(this.rolelist[i].id==data.roleid)
          {
            return this.rolelist[i].value;
          }
        }
      },
      NodeClick1(data) {
        // console.log(data);
        this.deptidselect=data.id;
        this.loadUsers();
      },
       NodeClick2(data) {
        // console.log('click data:'+JSON.stringify(data));
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
      handleSelectionChange(val) {
        this.multipleSelection = val;
      },
      handleNodeClick(data) {
        // console.log('click data:'+JSON.stringify(data));
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
        // console.log('submit!');
      },
      loadUsers(){
       var _this = this;
      this.tableLoading = true;
      // console.log(this.$store.userid);
      this.getRequest(
        "api/system/user/getUsers?page=" + this.currentPage + "&limit=10&deptid="+this.deptidselect
          //  +this.$store.userid
      ).then(resp => {
        this.tableLoading = false;
        // console.log(resp.data.data.rows);
        if (resp && resp.data.status == 200) {
          var data = resp.data.data;
          _this.accountlist = data.rows;
          _this.totalCount = data.total;
        }
      });
      },
      loadRoleMenu(){
        var _this =this;
        this.getRequest("/api/system/user/getrolemenu").then(resp=>{
          //console.log("***************************************"+JSON.stringify(resp.data));
              var data = resp.data;
              if (resp && resp.status ==200){
                _this.rolemenulist = data.data;
                // console.log(this.rolemenulist);
                //console.log("*********"+JSON.stringify(_this.depts));
              }
        })
      },
      loadRoles(){
        var _this =this;
        this.getRequest("/api/system/user/getrolelist").then(resp=>{
          //console.log("***************************************"+JSON.stringify(resp.data));
              var data = resp.data;
              if (resp && resp.status ==200){
                _this.rolelist = data.data;
                // console.log(this.rolelist);
                //console.log("*********"+JSON.stringify(_this.depts));
              }
        })
      },
      loadMenulist(){
        var _this = this;
        //this.getRequest("/system/basic/menuTree/-1").then(resp=> {
        this.getRequest("/api/menu/menuTree/-1").then(resp=> {
          // console.log('resp loadTreeData：'+JSON.stringify(resp.data));
          _this.treeLoading = false;
          if (resp && resp.status == 200) {
            _this.menulist = resp.data;
          }
        })
      },
      loadDepartments(){
        var _this =this;
        this.getRequest("/api/ndept/deptTree/"+this.$router.deptid).then(resp=>{
          //console.log("***************************************"+JSON.stringify(resp.data));
              var data = resp.data;
              if (resp && resp.status ==200){
                _this.departments = data.data;
                _this.departments2=data.data;
                console.log(_this.departments2);
                this.tableLoading=false;
                this.treeLoading=false;
                //console.log("*********"+JSON.stringify(_this.depts));
              }
        })
      },
      loadTreeData(){
        var _this = this;
        //this.getRequest("/system/basic/menuTree/-1").then(resp=> {
        this.getRequest("/api/menu/menuTree/-1").then(resp=> {
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
            // console.log('resp all：'+resp.data);
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
