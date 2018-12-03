<template>
  <div>
    <el-container>
      <div style="text-align: left">
        <el-dialog
          title="修改密码"
          :visible.sync="dialogVisible3"
          width="30%">
          <el-form :model="pwdForm" ref="pwdForm" class="pwdForm" :rules="rules2" label-width="100px">
            <el-form-item label="密码" prop="pass">
              <el-input type="password" v-model="pwdForm.pass" auto-complete="off"></el-input>
            </el-form-item>
            <el-form-item label="确认密码" prop="checkPass">
              <el-input type="password" v-model="pwdForm.checkPass" auto-complete="off"></el-input>
            </el-form-item>
            <el-form-item>
              <el-button type="primary" @click="onSubmitPwd('pwdForm')">提交</el-button>
              <el-button @click="resetForm('pwdForm')">重置</el-button>
            </el-form-item>
          </el-form>
        </el-dialog>
      </div>

      <div style="text-align: left">
        <el-dialog
          title="修改用户所在部门"
          :visible.sync="dialogVisible7"
          width="20%">
          <el-form :model="deptForm" ref="deptForm" class="deptForm" label-width="100px">
            <el-form-item>
              <treeselect
                style="width: 200px;"
                v-model="deptForm.deptid"
                :options="treeData"
                placeholder="请选择地区"
                :normalizer="normalizer"/>
            </el-form-item>
            <el-form-item>
              <el-button @click="cancelForm">取消</el-button>
              <el-button type="primary" @click="checkUserDept(deptForm)">修改</el-button>
            </el-form-item>
          </el-form>
        </el-dialog>
      </div>


      <div >
        <el-dialog
          title="修改网评员信息"
          :visible.sync="dialogVisible4"
          width="50%">
          <div style="margin-top: 10px;text-align: right" >
            <el-form :model="msgForm" status-icon :rules="rules" ref="msgForm" label-width="100px" class="msgForm">
              <el-row>
                <el-col :span="12">
                  <el-form-item label="用户名：" prop="name">
                    <el-input type="text" v-model="msgForm.name" :readonly="true"></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="12">
                  <el-form-item label="真实姓名：" prop="realName">
                    <el-input type="text" v-model="msgForm.realName"></el-input>
                  </el-form-item>
                </el-col>
              </el-row>
              <el-row>
                <el-col :span="12">
                  <el-form-item label="性别：" prop="sex">
                    <el-select v-model="msgForm.sex"  placeholder="请选择性別" prop="sex" style="width: 360px">
                      <el-option
                        v-for="item in sex"
                        :key="item.id"
                        :label="item.value"
                        :value="item.id">
                      </el-option>
                    </el-select>
                  </el-form-item>
                </el-col>
                <el-col :span="12">
                  <el-form-item label="生日：" prop="birthday" >
                    <el-date-picker
                      v-model="msgForm.birthday"
                      style="width: 360px;"
                      value-format="yyyy-MM-dd"
                      type="date"
                      placeholder="请选择时间">
                    </el-date-picker>
                  </el-form-item>
                </el-col>
              </el-row>
              <el-row>
                <el-col :span="12">
                  <el-form-item label="民族：" prop="nation">
                    <el-select v-model="msgForm.nation"  placeholder="请选择民族" prop="nation" style="width: 360px">
                      <el-option
                        v-for="item in nation"
                        :key="item.id"
                        :label="item.name"
                        :value="item.id">
                      </el-option>
                    </el-select>
                  </el-form-item>
                </el-col>
                <el-col :span="12">
                  <el-form-item label="电子邮箱：" prop="email">
                    <el-input type="text" v-model="msgForm.email"></el-input>
                  </el-form-item>
                </el-col>
              </el-row>
              <el-row>
                <el-col :span="12">
                  <el-form-item label="手机号码：" prop="tel">
                    <el-input type="text" v-model="msgForm.tel"></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="12">
                  <el-form-item label="选择角色：" prop="roleName">
                    <el-select v-model="msgForm.role"  placeholder="请选择角色" prop="role" style="width: 360px">
                      <el-option
                        v-for="item in role"
                        :key="item.id"
                        :label="item.value"
                        :value="item.id">
                      </el-option>
                    </el-select>
                  </el-form-item>
                </el-col>
              </el-row>
              <el-row>
                <el-col :span="12">
                  <el-form-item label="职位：" prop="job">
                    <el-input type="text" v-model="msgForm.job"></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="12">
                  <el-form-item label="QQ：" prop="qq">
                    <el-input type="text" v-model="msgForm.qq"></el-input>
                  </el-form-item>
                </el-col>
              </el-row>
              <el-row>
                <el-col :span="12">
                  <el-form-item label="微信：" prop="micro">
                    <el-input type="text" v-model="msgForm.micro"></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="12">
                  <el-form-item label="开户行：" prop="brankName">
                    <el-input type="text" v-model="msgForm.brankName"></el-input>
                  </el-form-item>
                </el-col>
              </el-row>
              <el-row>
              <el-col :span="12">
                <el-form-item label="银行账号：" prop="brankAccount">
                  <el-input type="text" v-model="msgForm.brankAccount"></el-input>
                </el-form-item>
              </el-col>
              <el-col :span="12">
                <el-form-item label="政治面貌：" prop="political">
                  <el-select v-model="msgForm.political"  placeholder="请选择政治面貌" prop="political" style="width: 360px">
                    <el-option
                      v-for="item in political"
                      :key="item.id"
                      :label="item.value"
                      :value="item.id">
                    </el-option>
                  </el-select>
                </el-form-item>
              </el-col>
            </el-row>
              <el-row>
                <el-col :span="12">
                  <el-form-item label="网评员属性：" prop="userType">
                    <el-select v-model="msgForm.userType"  placeholder="请选择网评员属性" prop="userType" style="width: 360px">
                      <el-option
                        v-for="item in userType"
                        :key="item.id"
                        :label="item.value"
                        :value="item.id">
                      </el-option>
                    </el-select>
                  </el-form-item>
                </el-col>
                <el-col :span="12">
                  <el-form-item label="文化程度：" prop="degree">
                    <el-select v-model="msgForm.degree"  placeholder="请选择文化程度" prop="degree" style="width: 360px">
                      <el-option
                        v-for="item in degree"
                        :key="item.id"
                        :label="item.value"
                        :value="item.id">
                      </el-option>
                    </el-select>
                  </el-form-item>
                </el-col>
              </el-row>

<!--              <el-form-item>
                <el-button type="primary" @click="submitForm('msgForm')">提交</el-button>
                <el-button @click="resetForm('msgForm')">重置</el-button>
              </el-form-item>-->
            </el-form>
          </div>
          <span slot="footer" class="dialog-footer">
                <el-button size="small" @click="dialogVisible4 = false">取消</el-button>
                <el-button size="small" type="primary" @click="submitMsgForm(msgForm)">修改</el-button>
              </span>
        </el-dialog>
      </div>

      <div >
        <el-dialog
          title="添加网评员信息"
          :visible.sync="dialogVisible6"
          width="50%">
          <div style="margin-top: 10px;text-align: right" >
            <el-form :model="msgForm" status-icon :rules="rules" ref="msgForm" label-width="100px" class="msgForm">
              <el-row>
                <!--<el-col :span="12">
                  <el-form-item label="用户名：" prop="name">
                    <el-input type="text" v-model="msgForm.name" :readonly="true"></el-input>
                  </el-form-item>
                </el-col>-->
                <el-col :span="12">
                  <el-form-item label="真实姓名：" prop="realName">
                    <el-input type="text" v-model="msgForm.realName"></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="12">
                  <el-form-item label="性别：" prop="sex">
                    <el-select v-model="msgForm.sex"  placeholder="请选择性別" prop="sex" style="width: 360px">
                      <el-option
                        v-for="item in sex"
                        :key="item.id"
                        :label="item.value"
                        :value="item.id">
                      </el-option>
                    </el-select>
                  </el-form-item>
                </el-col>
              </el-row>
              <el-row>

                <el-col :span="12">
                  <el-form-item label="生日：" prop="birthday" >
                    <el-date-picker
                      v-model="msgForm.birthday"
                      style="width: 360px;"
                      value-format="yyyy-MM-dd"
                      type="date"
                      placeholder="请选择时间">
                    </el-date-picker>
                  </el-form-item>
                </el-col>
                <el-col :span="12">
                  <el-form-item label="民族：" prop="nation">
                    <el-select v-model="msgForm.nation"  placeholder="请选择民族" prop="nation" style="width: 360px">
                      <el-option
                        v-for="item in nation"
                        :key="item.id"
                        :label="item.name"
                        :value="item.id">
                      </el-option>
                    </el-select>
                  </el-form-item>
                </el-col>
              </el-row>
              <el-row>

                <el-col :span="12">
                  <el-form-item label="电子邮箱：" prop="email">
                    <el-input type="text" v-model="msgForm.email"></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="12">
                  <el-form-item label="手机号码：" prop="tel">
                    <el-input type="text" v-model="msgForm.tel"></el-input>
                  </el-form-item>
                </el-col>

              </el-row>
              <el-row>

                <el-col :span="12">
                  <el-form-item label="选择角色：" prop="roleName">
                    <el-select v-model="msgForm.role"  placeholder="请选择角色" prop="role" style="width: 360px">
                      <el-option
                        v-for="item in role"
                        :key="item.id"
                        :label="item.value"
                        :value="item.id">
                      </el-option>
                    </el-select>
                  </el-form-item>
                </el-col>
                <el-col :span="12">
                  <el-form-item label="职位：" prop="job">
                    <el-input type="text" v-model="msgForm.job"></el-input>
                  </el-form-item>
                </el-col>
              </el-row>
              <el-row>

                <el-col :span="12">
                  <el-form-item label="QQ：" prop="qq">
                    <el-input type="text" v-model="msgForm.qq"></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="12">
                  <el-form-item label="微信：" prop="micro">
                    <el-input type="text" v-model="msgForm.micro"></el-input>
                  </el-form-item>
                </el-col>
              </el-row>
              <el-row>

                <el-col :span="12">
                  <el-form-item label="开户行：" prop="brankName">
                    <el-input type="text" v-model="msgForm.brankName"></el-input>
                  </el-form-item>
                </el-col>
                <el-col :span="12">
                  <el-form-item label="银行账号：" prop="brankAccount">
                    <el-input type="text" v-model="msgForm.brankAccount"></el-input>
                  </el-form-item>
                </el-col>
              </el-row>
              <el-row>

                <el-col :span="12">
                  <el-form-item label="政治面貌：" prop="political">
                    <el-select v-model="msgForm.political"  placeholder="请选择政治面貌" prop="political" style="width: 360px">
                      <el-option
                        v-for="item in political"
                        :key="item.id"
                        :label="item.value"
                        :value="item.id">
                      </el-option>
                    </el-select>
                  </el-form-item>
                </el-col>
                <el-col :span="12">
                  <el-form-item label="网评员属性：" prop="userType">
                    <el-select v-model="msgForm.userType"  placeholder="请选择网评员属性" prop="userType" style="width: 360px">
                      <el-option
                        v-for="item in userType"
                        :key="item.id"
                        :label="item.value"
                        :value="item.id">
                      </el-option>
                    </el-select>
                  </el-form-item>
                </el-col>
              </el-row>
              <el-row>

                <el-col :span="12">
                  <el-form-item label="备注信息：" prop="descript">
                    <el-input type="textarea" v-model="msgForm.descript"></el-input>
                  </el-form-item>
                </el-col>

                <el-col :span="12">
                  <el-form-item label="文化程度：" prop="degree">
                    <el-select v-model="msgForm.degree"  placeholder="请选择文化程度" prop="degree" style="width: 360px">
                      <el-option
                        v-for="item in degree"
                        :key="item.id"
                        :label="item.value"
                        :value="item.id">
                      </el-option>
                    </el-select>
                  </el-form-item>
                </el-col>
              </el-row>



              <!--              <el-form-item>
                              <el-button type="primary" @click="submitForm('msgForm')">提交</el-button>
                              <el-button @click="resetForm('msgForm')">重置</el-button>
                            </el-form-item>-->
            </el-form>
          </div>



          <span slot="footer" class="dialog-footer">
                <el-button size="small" @click="dialogVisible6 = false">取消</el-button>
                <el-button size="small" type="primary" @click="submitMsgForm1(msgForm)">添加</el-button>
              </span>
        </el-dialog>
      </div>


      <div >
        <el-dialog
          title="用户详细信息"
          :visible.sync="dialogVisible5"
          width="50%">
          <div style="text-align: right">
          <el-form :model="detailForm" ref="detailForm" label-width="100px">
            <el-row>
              <el-col :span="12">
                <el-form-item label="用户名：">
                  <el-input v-model="detailForm.name" :readonly="true"></el-input>
                </el-form-item>
              </el-col>
              <el-col :span="12">
                <el-form-item label="真实姓名：">
                  <el-input v-model="detailForm.realName" :readonly="true"></el-input>
                </el-form-item>
              </el-col>
            </el-row>
            <el-row>
              <el-col :span="12">
                <el-form-item label="性別：">
                  <el-input v-model="detailForm.sexValue" :readonly="true"></el-input>
                </el-form-item>
              </el-col>
              <el-col :span="12">
                <el-form-item label="出生年月：">
                  <el-input v-model="detailForm.birthday" :readonly="true"></el-input>
                </el-form-item>
              </el-col>
            </el-row>

            <el-row>
              <el-col :span="12">
                <el-form-item label="所在部门：">
                  <el-input v-model="detailForm.deptName" :readonly="true"></el-input>
                </el-form-item>
              </el-col>
              <el-col :span="12">
                <el-form-item label="用户角色：">
                  <el-input v-model="detailForm.roleName" :readonly="true"></el-input>
                </el-form-item>
              </el-col>
            </el-row>
            <el-row>
              <el-col :span="12">
                <el-form-item label="电子邮箱：">
                  <el-input v-model="detailForm.email" :readonly="true"></el-input>
                </el-form-item>
              </el-col>
              <el-col :span="12">
                <el-form-item label="手机号码：">
                  <el-input v-model="detailForm.tel" :readonly="true"></el-input>
                </el-form-item>
              </el-col>
            </el-row>
            <el-row>
              <el-col :span="12">
                <el-form-item label="民族：">
                  <el-input v-model="detailForm.nationName" :readonly="true"></el-input>
                </el-form-item>
              </el-col>
              <el-col :span="12">
                <el-form-item label="职位：">
                  <el-input v-model="detailForm.job" :readonly="true"></el-input>
                </el-form-item>
              </el-col>
            </el-row>
            <el-row>
              <el-col :span="12">
                <el-form-item label="QQ：">
                  <el-input v-model="detailForm.qq" :readonly="true"></el-input>
                </el-form-item>
              </el-col>
              <el-col :span="12">
                <el-form-item label="微信：">
                  <el-input v-model="detailForm.micro" :readonly="true"></el-input>
                </el-form-item>
              </el-col>
            </el-row>
            <el-row>
              <el-col :span="12">
                <el-form-item label="开户行：">
                  <el-input v-model="detailForm.brankName" :readonly="true"></el-input>
                </el-form-item>
              </el-col>
              <el-col :span="12">
                <el-form-item label="银行账号：">
                  <el-input v-model="detailForm.brankAccount" :readonly="true"></el-input>
                </el-form-item>
              </el-col>
            </el-row>
            <el-row>
              <el-col :span="12">
                <el-form-item label="政治面貌：">
                  <el-input v-model="detailForm.politicalValue" :readonly="true"></el-input>
                </el-form-item>
              </el-col>
              <el-col :span="12">
                <el-form-item label="文化程度：">
                  <el-input v-model="detailForm.degreeValue" :readonly="true"></el-input>
                </el-form-item>
              </el-col>
            </el-row>
            <el-row>
              <el-col :span="12">
                <el-form-item label="网评员属性：">
                  <el-input v-model="detailForm.userTypeValue" :readonly="true"></el-input>
                </el-form-item>
              </el-col>
              <el-col :span="12">

              </el-col>
            </el-row>


          </el-form>
          </div>
          <span slot="footer" class="dialog-footer">
                <el-button size="small" @click="dialogVisible5 = false">关闭</el-button>
          </span>
        </el-dialog>
      </div>
      <el-aside style="width: 20%;">
        <el-row>
          <div style="font-size: 14px;width: 200px;margin-top: 10px;text-align: left">
            <span >组织结构</span>
          </div>
        </el-row>
        <div>
          <!--default-expand-all-->
          <el-tree
            :props="defaultProps"
            :data="treeData"
            ref="tree"
            accordion
            :filter-node-method="filterNode"
            v-loading="treeLoading"
            highlight-current
            style="width: 300px"
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
                    v-for="item in allMenus"
                    :key="item.id"
                    :label="item.name"
                    :value="item.id">
                  </el-option>
                </el-select>
              </div>
              <div style="margin-top: 10px">
                <span>部门名称</span>
                <el-input size="mini" style="width: 200px;" v-model="menuName" placeholder="请输入部门名称..." @keyup.enter.native="addDep"></el-input>
              </div>
              <span slot="footer" class="dialog-footer">
                <el-button size="small" @click="doCancel">取消</el-button>
                <el-button size="small" type="primary" @click="addDep">添加</el-button>
              </span>
            </el-dialog>
          </div>

          <div style="text-align: left">
            <el-dialog
              title="修改部门"
              :visible.sync="dialogVisible2"
              width="25%">
              <div>
                <span>当前部门名</span>
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
                <span>新的部门名</span>
                <el-input size="mini" style="width: 200px;" v-model="menuName" placeholder="请输入部门名称..." @keyup.enter.native="addDep"></el-input>
              </div>
              <span slot="footer" class="dialog-footer">
                <el-button size="small" @click="doCancel">取消</el-button>
                <el-button size="small" type="primary" @click="updateDept">修改</el-button>
              </span>
            </el-dialog>
          </div>
        </div>
      </el-aside>
      <el-container style="width: 70%">
        <el-header style="width:100%;padding: 0px;justify-content:space-between;align-items: center">

          <div style="text-align: left;font-size: 14px;margin-top: 10px;">
            <el-row>
              <!--            <el-row>
         <div style="height:26px;font-size: 14px;margin-top: 10px;right: auto; left: 0px; top: 0px; width: 100px;background-color: lightgrey" >
         <span>部门的用户列表</span>
         <span style="color:red;margin-left:25px;">用户总数:{{this.userNumber}}</span>
         </div>
        </el-row>-->
              <span>用户总数:</span>
              <el-input
                :readonly="true"
                v-model="userNumber"
                style="width: 200px;margin: 0px;padding: 0px;color: red"
                size="mini">
              </el-input>
              <span>用户名:</span>
              <el-input
                placeholder="输入用户名..."
                style="width: 200px;margin: 0px;padding: 0px;"
                size="mini"
                prefix-icon="el-icon-search"
                v-model="username">
              </el-input>
              <span>真实姓名:</span>
              <el-input
                placeholder="输入姓名..."
                style="width: 200px;margin: 0px;padding: 0px;"
                size="mini"
                prefix-icon="el-icon-search"
                v-model="realName">
              </el-input>
              <span>手机号码:</span>
              <el-input
                placeholder="输入手机号码..."
                style="width: 200px;margin: 0px;padding: 0px;"
                size="mini"
                prefix-icon="el-icon-search"
                v-model="tel">
              </el-input>
              <el-button icon="el-icon-search" type="primary" size="mini" @click="searchQuery">搜索</el-button>
            </el-row>

          </div>
          <div style="text-align: left;font-size: 14px;margin-top: 10px;">
            <el-row>
              <el-button icon="el-icon-edit" size="mini" type="primary" @click="addWpy" plain round>添加网评员</el-button>
              <el-button icon="el-icon-delete" size="mini" type="primary" @click="deleteManyEmps" plain round>删除网评员</el-button>
              <el-button icon="el-icon-edit" size="mini" type="primary" @click="moveDept" plain round>移动部门</el-button>
              <el-upload
                :show-file-list="false"
                accept="application/vnd.ms-excel"
                action="/employee/basic/importEmp"
                :on-success="fileUploadSuccess"
                :on-error="fileUploadError" :disabled="fileUploadBtnText=='正在导入'"
                :before-upload="beforeFileUpload" style="display: inline">
                <el-button size="mini" type="success" :loading="fileUploadBtnText=='正在导入'">
                  <i class="fa fa-lg fa-level-up" style="margin-right: 5px"></i>
                  {{fileUploadBtnText}}
                </el-button>
              </el-upload>
              <el-button type="success" size="mini" @click="exportEmps">
                <i class="fa fa-lg fa-level-down" style="margin-right: 5px"></i>导出数据
              </el-button>
            </el-row>
          </div>
        </el-header>
      <el-main style="padding-left: 0px;padding-top: 30px">
        <div>
          <el-table
            :data="emps"
            v-loading="tableLoading"
            border
            stripe
            @selection-change="handleSelectionChange"
            size="mini"
            style="width: 100%;text-align: center">
            <el-table-column
              type="selection"
              align="center"
              width="30">
            </el-table-column>
            <el-table-column
              prop="index"
              align="center"
              fixed
              label="编号"
              width="50">
            </el-table-column>

            <el-table-column
              prop="name"
              align="center"
              fixed
              label="用户名"
              width="140">
            </el-table-column>
            <el-table-column
              prop="realName"
              width="140"
              align="center"
              label="真实姓名">
            </el-table-column>
            <el-table-column
              prop="deptName"
              align="center"
              label="所属部门"
              width="150">
            </el-table-column>
            <el-table-column
              prop="tel"
              width="150"
              align="center"
              label="手机号码">
            </el-table-column>
              <el-table-column
                prop="roleName"
                width="180"
                align="center"
                label="角色">
                <template slot-scope="scope">
                  <span v-if="scope.row.roleName != null || scope.row.roleName != ''">
                    {{scope.row.roleName}}
                  </span>
                  <span v-if="scope.row.roleName == null || scope.row.roleName == ''" style="color: red">暂无角色</span>
                </template>
              </el-table-column>

            <el-table-column
              prop="descript"
              width="180"
              align="center"
              label="备注">
            </el-table-column>
            <el-table-column
              prop="scope"
              width="300"
              align="center"
              label="操作">
              <template slot-scope="scope">
                <el-button type="text" @click="modifiPwd(scope.row.id)" style="color: gold" >修改密码</el-button>
                <el-button type="text" @click="modifiMsg(scope.row.id)" style="color: gold" >修改信息</el-button>
                <el-button type="text" @click="details(scope.row.id)" style="color: gold" >详情</el-button>
              </template>
            </el-table-column>
          </el-table>
          <div style="display: flex;justify-content: space-between;margin: 2px">
            <el-button type="text" style="color: red" size="mini" ></el-button>

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
      var validatePass = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('请输入密码'));
        } else {
          if (this.pwdForm.checkPass !== '') {
            this.$refs.pwdForm.validateField('checkPass');
          }
          callback();
        }
      };
      var validatePass2 = (rule, value, callback) => {
        if (value === '') {
          callback(new Error('请再次输入密码'));
        } else if (value !== this.pwdForm.pass) {
          callback(new Error('两次输入密码不一致!'));
        } else {
          callback();
        }
      };

      return {
        deptid: '1',
        fid: '0',
        sex: [{id:'0',value:'女'},{id:'1',value:'男'}],
        nation: [],
        role: [],
        userType: [],
        degree: [],
        political: [],

        userNumber: '',
        emps: [],
        totalCount: -1,
        currentPage: 1,
        multipleSelection: [],
        fileUploadBtnText: '导入数据',
        keywords: '',
        username: '',
        realName: '',
        tel: '',
        search: '0',
        menuName: '',
        treeLoading: false,
        dialogVisible: false,
        dialogVisible2: false,
        dialogVisible3: false,
        dialogVisible4: false,
        dialogVisible5: false,
        dialogVisible6: false,
        dialogVisible7: false,
        tableLoading: false,
        allMenus: [],
        pDep: '',
        pDep1: '',
        treeData: [],
        defaultProps: {
          label: 'name',
          isLeaf: 'leaf',
          children: 'children'
        },
        normalizer(node) {
          return {
            id: node.id,
            label: node.name,
            children: node.children,
          }
        },

        deptForm: {
          ids: '',
          deptid: null,
        },
        pwdForm: {
          id: '',
          pass: '',
          checkPass: ''
        },
        detailForm: {
          deptName: '',
          name: '',
          realName: '',
          sexValue: '',
          birthday: '',
          nationName: '',
          email: '',
          tel: '',
          roleName: '',
          job: '',
          qq: '',
          micro: '',
          brankName: '',
          brankAccount: '',
          politicalValue: '',
          userTypeValue: '',
          degreeValue: '',
        },
        msgForm: {
          deptid: '',
          fid: '',
          name: '',//用户名
          realName: '',//真实姓名
          sex: [],//性别
          birthday: '',//生日
          nation: [],//民族
          email: '',//邮箱
          tel: '',//电话
          role: [],//角色
          job: '',//职位
          qq: '',
          micro: '',//微信
          brankName: '',//开户行
          brankAccount: '',//银行卡号
          political: [],//政治面貌
          userType: [],//网评员属性
          degree: [],//文化程度
          descript: '',
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
          realName:[
            {required:true,message:'请输入姓名',trigger:'blur'},
          ]
        },
        rules2: {
          pass: [
            { validator: validatePass, trigger: 'blur' }
          ],
          checkPass: [
            { validator: validatePass2, trigger: 'blur' }
          ],
        }
      }
    },
    mounted: function () {
      this.treeLoading = true;
      this.loadTreeData();
      //this.loadAllDeps();
      //this.loadEmps();
      this.initDataUserNum();
      this.initDataUser();
      //民族
      this.initData();
      console.log("deptid " +this.deptid);
    },
    watch: {
      keywords(val) {
        this.$refs.tree.filter(val);
      }
    },
    methods: {
      fileUploadSuccess(response, file, fileList){
        if (response) {
          this.$message({type: response.status, message: response.msg});
        }
        this.loadEmps();
        this.fileUploadBtnText = '导入数据';
      },
      fileUploadError(err, file, fileList){
        this.$message({type: 'error', message: "导入失败!"});
        this.fileUploadBtnText = '导入数据';
      },
      beforeFileUpload(file){
        this.fileUploadBtnText = '正在导入';
      },
      filterNode(value, data) {
        if (!value) return true;
        return data.name.indexOf(value) !== -1;
      },
      handleNodeClick(data) {
        this.initDataUserNum(data.id);
        this.initDataUser(data.id);
        this.deptid = data.id;
        this.fid = data.fid;
        console.log('click data:'+JSON.stringify(data));
        console.log("deptid  "+JSON.stringify(data.id));
      },
      currentChange(currentChange){
        this.currentPage = currentChange;
        this.initDataUser();
      },
      handleSelectionChange(val) {
        this.multipleSelection = val;
      },
      exportEmps(){
        //window.open("/employee/basic/exportEmp", "_parent");
      },
      onSubmitUserDept(data){

        console.log("data"+JSON.stringify(data));
      },
      onSubmitPwd(data) {
        var _this = this;
        this.putRequestJson("api/user/modifyUserSubPwd",this.pwdForm).then(resp=>{
          if(resp && resp.status == 200 ){
            _this.$message({type: resp.data.status, message: resp.data.msg});
            this.dialogVisible3 = false;
          }else {
            _this.$message({type: resp.status, message: resp.msg});
          }
        })
      },
      submitMsgForm(msgForm){
        var _this = this;
        this.putRequestJson("api/user/updateUser",this.msgForm).then(resp=>{
            if(resp && resp.status == 200){
              console.log("回  "+JSON.stringify(resp));
              _this.$message({type:resp.status,message:"修改成功"});
              _this.dialogVisible4  = false;
              _this.initDataUser();
            }
        })
      },
      submitMsgForm1(msgForm){
        var _this = this;
        msgForm.deptid = this.deptid;
        msgForm.fid = this.fid;
        this.postRequestJson("api/user/addUser",this.msgForm).then(resp=>{
          if(resp && resp.status == 200){
            _this.dialogVisible6  = false;
            var data = resp.data;
            //console.log("回  "+JSON.stringify(resp));
            _this.$message({type:data.status,message:data.message});
            _this.initDataUser(msgForm.deptid);
          }
        })
      },
      cancelForm(){
        this.deptForm.deptid = null;
        this.dialogVisible7 = false;
      },
      modifiPwd(data){
        this.dialogVisible3 =true;
        this.pwdForm.id = data;
          console.log("修改  "+data);
      },
      modifiMsg(id){
        this.dialogVisible4 =true;
        this.getUserDetail(id);
        console.log("修改信息  " +id);
      },
      details(id){
        this.dialogVisible5 =true;
        this.getUserDetail1(id);
        console.log("详情");
      },
      addWpy(){
          this.dialogVisible6 = true;
          console.log("添加网评员");
      },
      moveDept(){
        this.dialogVisible7 = true;
      },
      checkUserDept(deptForm){
        var _this = this;
        var ids = '';
        for (var i = 0; i < this.multipleSelection.length; i++) {
          ids += this.multipleSelection[i].id + ",";
        }
        if(ids == ''||ids == null){
          this.$message({type:'warning',message:"请选择要修改的用户"});
        }
        deptForm.ids = ids;
        this.putRequestJson("api/user/updateDept",deptForm).then(resp=>{
          this.dialogVisible7 = false;
            if (resp && resp.status == 200){
              var data = resp.data;
              _this.$message({type: data.status, message: data.message});
            }
        })
        console.log("deptid**** "+_this.deptid);
        _this.initDataUser(_this.deptid);
        console.log("ids  "+ids);
        console.log("deptid "+deptForm.deptid);
      },
      submitForm(formName) {
        this.$refs[formName].validate((valid) => {
          if (valid) {
            alert('submit!');
          } else {
            console.log('error submit!!');
            return false;
          }
        });
      },
      resetForm(formName){
        this.$refs[formName].resetFields();
      },

      searchQuery(){
        this.initDataUser();
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
      initDataUserNum(deptid){
        if(deptid == undefined){//初始化
          deptid = 1;
        }
        var _this = this;
          this.getRequest("api/user/getUnitsubAllUserNumber?deptid="+deptid).then(resp=>{
            if(resp && resp.status == 200){
              this.userNumber = resp.data.data;
            }
          })
      },
      initDataUser(node){
        if(node == undefined){
          node = 1;
        }else {
          this.search = '1';
        }
        if(this.username != null || this.realName != null || this.tel != null ){
          this.search = '1';
        }

       console.log("search   "+this.search);
        var _this = this;
        this.getRequest("api/user/getUnitUser?node="+node+"&search="+this.search+"&username="+this.username+"&realName="+this.realName+"&tel="+this.tel+"&page="+this.currentPage+"&limit=10").then(resp=>{
          if(resp && resp.status == 200 ){
            var data = resp.data.data;
            _this.emps = data.rows;
            _this.totalCount = data.total;
          }
        })
      },
      initData(){
        var _this = this;
        //民族
        this.getRequest("api/user/getNation").then(resp=>{
          if(resp && resp.status == 200 ){
            _this.nation = resp.data.data;
          }
        })
        //角色
        this.getRequest("api/rightManager/getRole").then(resp=>{
          if(resp && resp.status == 200 ){
            _this.role = resp.data.data;
          }
        })
       //政治面貌
        this.getRequest("api/user/getPolitical").then(resp=>{
          if(resp && resp.status == 200 ){
            _this.political = resp.data.data;
          }
        })
        //网评员属性
        this.getRequest("api/user/getUserType").then(resp=>{
          if(resp && resp.status == 200 ){
            _this.userType = resp.data.data;
          }
        })
       //文化程度
        this.getRequest("api/user/getDegree").then(resp=>{
          if(resp && resp.status == 200 ){
            _this.degree = resp.data.data;
          }
        })
      },
      getUserDetail(id){
        var _this = this;
        this.getRequest("api/user/getDetail?id="+id).then(resp=>{
            if(resp && resp.status == 200){
              var data = resp.data.data;
              _this.msgForm = data;
            }

        })
      },
      getUserDetail1(id){
        var _this = this;
        this.getRequest("api/user/getDetail?id="+id).then(resp=>{
          if(resp && resp.status == 200){
            var data = resp.data.data;
            _this.detailForm = data;
          }
        })
      },

      loadEmps(){
        var _this = this;
        this.tableLoading = true;
          this.getRequest("api/report/queryorder?page=" + this.currentPage + "&limit=10&name=" + this.keywords ).then(resp=> {
          this.tableLoading = false;
          //console.log("***********"+JSON.stringify(resp));
          if (resp && resp.data.status == 200) {
            var data = resp.data.data;
            _this.emps = data.rows;
            _this.totalCount = data.total;
          }
        })
      },
      loadTreeData(){
        var _this = this;
        //this.getRequest("/system/basic/menuTree/-1").then(resp=> {
        //    /api/ndept/deptTree/1
        this.getRequest("/api/ndept/deptTree/1").then(resp=> {
          //console.log('resp loadTreeData：'+JSON.stringify(resp.data));
          _this.treeLoading = false;
          if (resp && resp.status == 200) {
            _this.treeData = resp.data.data;
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
      doCancel(){
        this.dialogVisible = false;
        this.dialogVisible2 = false;
        this.menuName = '';
        this.pDep1 = '';
      },
      addDep(){
        var _this = this;
        this.dialogVisible = false;
        this.treeLoading = true;
        this.postRequestJson("api/ndept/addDept", {
          name: this.menuName,
          deptid: this.pDep1
        }).then(resp=> {
          _this.treeLoading = false;
          if (resp && resp.status == 200) {
            var data = resp.data;
            _this.$message({type: data.status, message: data.message});
            _this.loadTreeData();
  /*          _this.menuName = '';
            _this.setDataToTree(_this.treeData,_this.pDep,respData.msg)*/
          }
        })
      },
      updateDept(){//修改部门
        var _this = this;
        this.dialogVisible = false;
        this.treeLoading = true;
        this.putRequestJson("api/ndept/updateDept",{
          name: this.menuName,
          deptid: this.pDep1
        }).then(resp=>{
          if(resp && resp.status == 200){
            var data = resp.data;
            _this.dialogVisible2 = false;
            _this.$message({type: data.status, message: data.message});
            _this.loadTreeData();
          }
        })
      },
      loadAllDeps(id){
        var _this = this;
        console.log("id  "+id);
        this.getRequest("api/ndept/showDept?id="+id).then(resp=> {
          //console.log("*****       "+JSON.stringify(resp))
          if (resp && resp.status == 200) {
            console.log('*** ：'+JSON.stringify(resp.data.data));
            _this.allMenus = resp.data.data;
          }
        });
      },
      showAddDepView(data, event){//添加部门
        console.log("data "+JSON.stringify(data));
        this.loadAllDeps(data.id);
        this.dialogVisible = true;
        this.pDep = data.name;
        this.pDep1 = data.id;
        event.stopPropagation()
      },
      modifiedDep(data,event){//修改部门
        //this.loadAllDeps(data.id);
        //this.allMenus = this.treeData;
        this.dialogVisible2 = true;
        this.pDep = data.name;
        this.pDep1 = data.id;
        event.stopPropagation()
      },
      deleteManyEmps(){
        this.$confirm('此操作将删除[' + this.multipleSelection.length + ']条数据, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          var ids = '';
          for (var i = 0; i < this.multipleSelection.length; i++) {
            ids += this.multipleSelection[i].id + ",";
          }
          console.log("ids  "+ids);
          this.doDelete(ids);
        }).catch(() => {
        });
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
        this.deleteRequest("api/user/deleteUser/" + ids).then(resp=> {
          _this.tableLoading = false;
          if (resp && resp.status == 200) {
            var data = resp.data;
            _this.$message({type: data.status, message: data.message});
            _this.initDataUser(this.deptid);
           // _this.loadEmps();
          }
        })
      },
      deleteDep(data, event){
        var _this = this;
        if (data.children.length>0) {
          console.log("***"+data.children.length);
          this.$message({
            message: '该部门下尚有其他子部门，不能被删除!',
            type: 'warning'
          });
        } else {
          this.$confirm('删除[ ' + data.name + ' ]部门的同时会删除该部门下的人员信息(包括网评员及其网站账号),您确定要删除吗？', '提示', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            type: 'warning'
          }).then(() => {
            _this.treeLoading = true;
            console.log("部门ID "+data.id);
            _this.deleteRequest("api/ndept/deleteDept/" + data.id).then(resp=> {
              _this.treeLoading = false;
              if (resp && resp.status == 200) {
                var respData = resp.data;
                _this.$message({
                  message: respData.message,
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
        <el-button style="font-size: 12px;" icon="el-icon-edit" type="primary" plain round size="mini" style="padding:3px" on-click={ () => this.showAddDepView(data,event) }>添加</el-button>
        <el-button style="font-size: 12px;" icon="el-icon-edit" type="success" plain round size="mini" style="padding:3px" on-click={ () => this.modifiedDep(data,event) }>修改</el-button>
        <el-button style="font-size: 12px;" icon="el-icon-delete" type="danger" plain round size="mini" style="padding:3px" on-click={ () => this.deleteDep(data,event) }>删除</el-button>
        </span>
        </span>);
      }
    }
  };
</script>
<style lang="stylus" rel="stylesheet/stylus">
  //@import './DepMana.styl';

</style>
