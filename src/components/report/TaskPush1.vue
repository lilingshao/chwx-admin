<template>
  <el-row style="margin-top: 15px">
    <el-col :span="12">
      <h3>任务上报</h3>
      <el-form :model="pushForm" :rules="rules" ref="pushForm" label-width="100px">
        <el-form-item label="任务类型：" prop="type">
  <!--        <el-checkbox-group v-model="pushForm.checklist" :max="1"  >
            <el-checkbox :label="report" v-for="report in pushForm.reports" :key="report">{{report}}</el-checkbox>
          </el-checkbox-group>
-->
        <el-radio-group v-model="pushForm.radio2" @change="clickHandler">
          <el-radio :label="3" >日常任务</el-radio>
          <el-radio :label="6" >特色任务</el-radio>
          <el-radio :label="9" >舆情上报</el-radio>
        </el-radio-group>
        </el-form-item>

        <el-form-item label="类型详细：" prop="typeBig">
          <el-select v-model="pushForm.typeBig" style="width: 590px" placeholder="请选择" >
            <el-option
              v-for="item in typeBig"
              :key="item.id"
              :label="item.value"
              :value="item.id">
            </el-option>
          </el-select>
        </el-form-item>
        <el-form-item label="任务地址：" prop="address">
          <el-input v-model="pushForm.address"></el-input>
        </el-form-item>
        <el-form-item label="任务描述：" prop="markto">
          <el-input v-model="pushForm.markto" :onfocus="focus"></el-input>
        </el-form-item>
        <!--action="/api/file/upload"-->
        <el-form-item label="上传截图：" prop="component">
          <el-upload
            action="/api/file/upload"
            list-type="picture-card"
            :on-success="handleAvatarSuccess"
            :before-upload="beforeAvatarUpload"
            :on-remove="handleRemove"
            :file-list="files">

            <i class="el-icon-plus"></i>
          </el-upload>
          <el-dialog :visible.sync="dialogVisible">
            <img width="100%" :src="dialogImageUrl" alt="">
          </el-dialog>

          <!--<el-upload
            class="avatar-uploader"
            action="https://jsonplaceholder.typicode.com/posts/"
            :show-file-list="false"
            :on-success="handleAvatarSuccess"
            :before-upload="beforeAvatarUpload">
            <img v-if="imageUrl" :src="imageUrl" class="avatar">
            <i v-else class="el-icon-plus avatar-uploader-icon"></i>
          </el-upload>-->

        </el-form-item>

        <el-form-item>
          <el-button type="primary" @click="onSubmit('pushForm')">提交</el-button>
          <el-button @click="emptyFormData" type="danger">取消</el-button>
        </el-form-item>
      </el-form>
    </el-col>
  </el-row>
</template>
<script>
  export default {
    data() {
      return {
        files: [],
        imageUrl: '',
        dialogImageUrl: '',
        dialogVisible: false,
        keywords: '',
        menuName: '',
        treeLoading: false,
        dialogVisible: false,
        typeBig: [],
        pDep: '',
        treeData: [],
        defaultProps: {
          label: 'name',
          isLeaf: 'leaf',
          children: 'children'
        },
        pushForm:{
          type: '',
          typeBig: [],
          radio2: 3,
          address: '',
          markto: '',
          files: [],
         // checkd: true,
         // name: '',
         // enabled: false,
         // type: [],
          component: '',
         // iconCls: '',
        },
        rules:{
          typeBig: [{required: true, message: '必填:类型详细', trigger: 'change'}],
          address: [{required: true, message: '必填:人物地址', trigger: 'blur'}],
          markto: [{required: true, message: '必填:任务描述', trigger: 'blur'}],
          //component: [{required: true, message: '必填:截图', trigger: 'blur'}],
        }
      }
    },
    mounted: function () {
      this.treeLoading = true;
      this.loadTreeData();
      this.loadtypeBig();
    },
    watch: {
      keywords(val) {
        this.$refs.tree.filter(val);
      }
    },
    methods: {
      focus(){
        console.log("获取焦点");
      },
      clickHandler(value){
        //console.log("改变后的值是:"+value);
        if(value==3){
          var _this = this;
          this.getRequest("/api/reportType/getBigType?type=2").then(resp=> {
            if (resp && resp.status == 200) {
              _this.typeBig = resp.data.data;
            }
          });
          //console.log(JSON.stringify(_this.typeBig))
          return;
        }else if(value == 6){
          var _this = this;
          this.getRequest("/api/reportType/getBigType?type=10").then(resp=> {
            if (resp && resp.status == 200) {
              _this.typeBig = resp.data.data;
            }
          });
          //console.log(JSON.stringify(_this.typeBig))
          return;
        }else if(value == 9){
          var _this = this;
          this.getRequest("/api/reportType/getBigType?type=27").then(resp=> {
            if (resp && resp.status == 200) {
              _this.typeBig = resp.data.data;
            }
          });
          //console.log(JSON.stringify(_this.typeBig))
          return;
        }
      },
      emptyFormData(){
        this.pushForm.radio2 = 3;
        this.pushForm.address = '';
        this.pushForm.markto = '';
        this.pushForm.typeBig = '';
      },
      handleRemove(file, fileList) {
        console.log(file, fileList);
      },
      handleAvatarSuccess(res, file) {
        this.imageUrl = URL.createObjectURL(file.raw);
      },
      beforeAvatarUpload(file) {
        const isIMAGE = file.type === 'image/jpeg'||'image/png';
        const isLt2M = file.size / 1024 / 1024 < 2;

        if (!isIMAGE) {
          this.$message.error('上传图片只能是 JPG或者PNG格式!');
        }
        if (!isLt2M) {
          this.$message.error('上传图片大小不能超过 2MB!');
        }
        return isIMAGE && isLt2M;
      },
      filterNode(value, data) {
        if (!value) return true;
        return data.name.indexOf(value) !== -1;
      },
      handleNodeClick(data) {
       // console.log('click data:'+JSON.stringify(data));
       // this.pushForm.id=data.id;
       // this.pushForm.name=data.name;
       // this.pushForm.address=data.address;
       // this.pushForm.url=data.url;
       // this.pushForm.component=data.component;
       // this.pushForm.iconCls=data.iconCls;
       // this.pushForm.parentId=data.parentId;
       // this.pushForm.enabled=data.enabled;
       // this.pushForm.typeBig = data.typeBig;
      },
      onSubmit(formData) {
        var _this = this;
        this.$refs[formData].validate((valid) => {
          if (valid) {
            //添加
            this.tableLoading = true;
            var _this = this;
            console.log('submit form:'+JSON.stringify(formData));
            this.postRequestJson("api/reportTask/taskReport", this.pushForm).then(resp=> {
              if (resp && resp.status == 200) {
                console.log(JSON.stringify(resp));
                _this.$message({type: resp.data.status, message: "任务上报"+resp.data.msg});
                _this.emptyFormData();
               // console.log("**************任务发布成功!!!")
              }else {
                _this.$message({type: resp.data.status, message: resp.data.message});
              }
            })
          } else {
            return false;
          }
        });
        
      },
      loadTreeData(){
        var _this = this;
        //this.getRequest("/system/basic/menuTree/-1").then(resp=> {
        this.getRequest("/api/menu/menuTree/-1").then(resp=> {
          //console.log('resp loadTreeData：'+JSON.stringify(resp.data));
          _this.treeLoading = false;
          if (resp && resp.status == 200) {
            _this.treeData = resp.data;
          }
        })
      },
      setDataToTree(treeData,pId,respData){
        //console.log('respData：'+JSON.stringify(respData));
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
      loadtypeBig(){
        var _this = this;
        this.getRequest("/api/reportType/getBigType?type=2").then(resp=> {
          if (resp && resp.status == 200) {
            _this.typeBig = resp.data.data;
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
 /* .avatar-uploader .el-upload {
    border: 1px dashed #d9d9d9;
    border-radius: 6px;
    cursor: pointer;
    position: relative;
    overflow: hidden;
  }
  .avatar-uploader .el-upload:hover {
    border-color: #409EFF;
  }
  .avatar-uploader-icon {
    font-size: 28px;
    color: #8c939d;
    width: 178px;
    height: 178px;
    line-height: 178px;
    text-align: center;
  }
  .avatar {
    width: 178px;
    height: 178px;
    display: block;
  }*/
  //@import './DepMana.styl';
</style>
