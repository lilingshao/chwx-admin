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
          <el-input v-model="pushForm.markto" ></el-input>
        </el-form-item>

        <el-form-item label="上传截图：" prop="component">

          <el-upload
            class="upload-demo"
            name="file"
            ref="upload"
            action="/api/file/upload"
            :on-preview="handlePreview"
            :on-remove="handleRemove"
            :on-success="uploadOnSuccess"
            :on-error="uploadOnError"
            :before-upload="beforeUpload"
            :auto-upload="false">
            <el-button slot="trigger" size="small" type="primary">选取文件</el-button>
            <!--<el-button style="margin-left: 10px;" size="small" type="success" @click="submitUpload">上传到服务器</el-button>-->
            <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过500kb</div>
          </el-upload>
        </el-form-item>

        <el-form-item>
          <el-button type="primary" @click="onSubmit(pushForm)">提交</el-button>
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
        lastReportId: '',
        fileList: [],
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
          fileList: [],
          number: 0,
          component: '',
        },
        rules:{
          typeBig: [{required: true, message: '必填:类型详细', trigger: 'change'}],
          address: [{required: true, message: '必填:任务地址', trigger: 'blur'}],
          markto: [{required: true, message: '必填:任务描述', trigger: 'blur'}],
          //component: [{required: true, message: '必填:截图', trigger: 'blur'}],
        }
      }
    },
    mounted: function () {
      this.treeLoading = true;
      //this.loadTreeData();
      this.loadtypeBig();
    },
    watch: {
      keywords(val) {
        this.$refs.tree.filter(val);
      }
    },
    methods: {
      clickHandler(value){
        //console.log("改变后的值是:"+value);
        if(value==3){
          var _this = this;
          this.getRequest("/api/reportType/getBigType?type=2").then(resp=> {
            if (resp && resp.status == 200) {
              _this.typeBig = resp.data.data;
            }
          });
          return;
        }else if(value == 6){
          var _this = this;
          this.getRequest("/api/reportType/getBigType?type=10").then(resp=> {
            if (resp && resp.status == 200) {
              _this.typeBig = resp.data.data;
            }
          });
          return;
        }else if(value == 9){
          var _this = this;
          this.getRequest("/api/reportType/getBigType?type=27").then(resp=> {
            if (resp && resp.status == 200) {
              _this.typeBig = resp.data.data;
            }
          });
          return;
        }
      },
      emptyFormData(){
        this.pushForm.radio2 = 3;
        this.pushForm.address = '';
        this.pushForm.markto = '';
        this.pushForm.typeBig = '';
        this.pushForm.component = '';
        //清空图片
        this.$refs.upload.clearFiles();

      },
      uploadOnSuccess(response,file,fileList) {

        console.log("*****"+JSON.stringify(response));
      },
      uploadOnError(){
        this.$message.warning("图片上传失败!");
      },
      beforeUpload(file){
        let Xls = file.name.split('.');
        if(Xls[1] === 'jpg'||Xls[1] === 'png'){
          return file
        }else {
          this.$message.error('上传文件只能是 jpg/png 格式!')
          return false
        }
      },
      handleRemove(file, fileList) {
        console.log(file, fileList);
      },
      handlePreview(file) {
        console.log(file);
      },
      filterNode(value, data) {
        if (!value) return true;
        return data.name.indexOf(value) !== -1;
      },
      onSubmit(formData) {
        var _this = this;
        this.$refs.upload.submit();
        this.pushForm.number = this.$refs.upload.uploadFiles.length;

        if (formData.typeBig==''||formData.address==''||formData.markto==''){
          return false;
        }

        if(this.pushForm.number != 0){
          this.submitFormData(formData);
        }else {
          var _this = this;
          this.$message.error("请选择图片");
        }
      },
      submitFormData(formData){
        this.tableLoading = true;
        var _this = this;
        this.postRequestJson("api/reportTask/add", this.pushForm).then(resp=> {
          if (resp && resp.status == 200) {
            var data = resp.data;
            _this.$message({type: data.status, message: data.msg});
            _this.emptyFormData();
          }else {
            _this.$message({type:201,message: "任务上报失败!"});
          }
        })
      },
        /*this.$refs[_this.pushForm].validate((valid) => {
          //this.pushForm.number = this.$refs.upload.uploadFiles.length;
          if (valid) {
            //添加
            this.tableLoading = true;
            var _this = this;
            this.postRequestJson("api/reportTask/taskReport", this.pushForm).then(resp=> {
              if (resp && resp.status == 200) {
                var data = resp.data;
                _this.$message({type: data.status, message: data.msg});
                _this.emptyFormData();
              }else {
                _this.$message({type:201,message: "任务上报失败!"});
              }
            })
          } else {
            return false;
          }
        });*/

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
            console.log("typeBig  "+JSON.stringify(_this.typeBig));

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
    }
  };
</script>
<style lang="stylus" rel="stylesheet/stylus">
</style>
