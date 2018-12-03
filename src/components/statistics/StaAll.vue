<template>
  <el-row style="margin-top: 15px">
    <el-col :span="12">
      <el-form :model="pushForm" :rules="rules" ref="pushForm" label-width="100px">
        <el-upload
          class="upload-demo"
          action="http://localhost:8089/upload"
          :on-preview="handlePreview"
          :on-remove="handleRemove"
          :on-exceed="handleExceed"
          :file-list="fileList2"
          :multiple="true"
          :headers=headers
          :limit="3"
          list-type="picture">
          <el-button size="small" type="primary">点击上传</el-button>
          <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过500kb</div>
        </el-upload>
      </el-form>
    </el-col>
  </el-row>
</template>
<script>
  export default {
    data() {
      return {
        headers: {
          'Content-Type': 'multipart/form-data' //请求头
        },
        fileList2: [{name: 'food.jpeg', url: 'http://localhost:8089/upload/931065a2-44ac-449e-a8e5-1bbfbaa58295.jpg'}, {name: 'food2.jpeg', url: 'https://fuss10.elemecdn.com/3/63/4e7f3a15429bfda99bce42a18cdd1jpeg.jpeg?imageMogr2/thumbnail/360x360/format/webp/quality/100'}],
        radio2: 3,
        dialogImageUrl: '',
        dialogVisible: false,
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
        pushForm:{
          checkd: true,
          // checklist: ['日常任务'],
          //reports: ['日常任务','特色任务','舆情上报'],
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
      handleRemove(file, fileList) {
        console.log(file, fileList);
      },
      handlePreview(file) {
        console.log(file);
      },
      handleExceed(){
        console.log("文件数量超出");
      },
      handlePictureCardPreview(file) {
        this.dialogImageUrl = file.url;
        this.dialogVisible = true;
      },
      filterNode(value, data) {
        if (!value) return true;
        return data.name.indexOf(value) !== -1;
      },
      handleNodeClick(data) {
        console.log('click data:'+JSON.stringify(data));
        this.pushForm.id=data.id;
        this.pushForm.name=data.name;
        this.pushForm.path=data.path;
        this.pushForm.url=data.url;
        this.pushForm.component=data.component;
        this.pushForm.iconCls=data.iconCls;
        this.pushForm.parentId=data.parentId;
        this.pushForm.enabled=data.enabled;
        this.pushForm.upDep = data.parentId;
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
        this.postRequest("/system/basic/dep", {
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
