<template>
   <section>
      <!--显示菜单路径-->
      <el-col :span="24" class="warp-breadcrum">
         <el-breadcrumb separator="/">
            <!-- <el-breadcrumb-item :to="{ path: '/' }"><b>首页</b></el-breadcrumb-item> -->
            <!-- <el-breadcrumb-item>用户信息</el-breadcrumb-item> -->
         </el-breadcrumb>
      </el-col>
      <!--工具条-->
      <el-col :span="24" class="toolbar" style="padding-bottom: 0;">
         <el-form :inline="true" :model="filters">
            <el-form-item>
               <el-input v-model.trim="filters.name" clearable placeholder="请输入查询内容"></el-input>
            </el-form-item>
            <el-form-item>
               <el-button type="primary" @click="getUsersList">查询</el-button>
            </el-form-item>
            <el-form-item>
               <el-button type="primary" @click="handleAdd">新增</el-button>
            </el-form-item>
            <!-- <el-form-item>
             <el-button type="primary" @click="outputExcel" >导出excel</el-button>
           </el-form-item> -->
         </el-form>
      </el-col>

      <!--列表-->
      <el-table class="master_table" center :data="users" highlight-current-row tooltip-effect="dark" v-loading="listLoading" style="width: 100%;">
         <!--<el-table-column type="selection" width="50"></el-table-column>-->
         <!--<el-table-column type="index" width="50"></el-table-column>-->
         <el-table-column prop="userId" label="用户ID" min-width="100" show-overflow-tooltip></el-table-column>
         <!--<el-table-column prop="userUUID" label="用户UUID" min-width="100" show-overflow-tooltip></el-table-column>-->
         <el-table-column prop="userRealName" label="真实姓名" min-width="100" show-overflow-tooltip></el-table-column>
         <el-table-column prop="userEmail" label="用户邮箱" min-width="100" show-overflow-tooltip></el-table-column>
         <!--<el-table-column prop="userPassword" label="用户密码" min-width="100" show-overflow-tooltip></el-table-column>-->
         <el-table-column prop="userCompany" label="公司名称" min-width="100" show-overflow-tooltip></el-table-column>
         <el-table-column prop="userDept" label="部门名称" min-width="100" show-overflow-tooltip></el-table-column>
         <!--<el-table-column prop="userLine" label="条线名称" min-width="100" sortable show-overflow-tooltip></el-table-column>-->
         <!--<el-table-column prop="createPerson" label="创建人" min-width="100" sortable show-overflow-tooltip></el-table-column>-->
         <!--<el-table-column prop="createTime" label="创建时间" min-width="120" sortable show-overflow-tooltip></el-table-column>-->
         <!--<el-table-column prop="updataPerson" label="修改人" min-width="100" sortable show-overflow-tooltip></el-table-column>-->
         <!--<el-table-column prop="updataTime" label="修改时间" min-width="120" sortable show-overflow-tooltip></el-table-column>-->
         <el-table-column label="操作" width="150">
            <template slot-scope="scope">
               <el-button type="primary" size="small" @click="findUserById(scope.row)">修改</el-button>
               <el-button type="danger" size="small" @click="handleDel(scope.row)">删除</el-button>
            </template>
         </el-table-column>
      </el-table>

      <!--工具条-->
      <el-col :span="24" class="toolbar">
         <el-pagination layout="prev, pager, next" @current-change="handleCurrentChange" :page-size="pageSize" :total="total" style="float:right;"></el-pagination>
      </el-col>

      <!--编辑界面-->
      <el-dialog title="修改用户信息" center :visible.sync="editFormVisible" :close-on-click-modal="false">
         <el-form :model="editForm" label-width="100px" :rules="addFormRules" ref="editForm">
            <div class="addTeaInfoList">
               <el-form-item label="真实姓名" prop="userRealName">
                  <el-input v-model.trim="editForm.userRealName" auto-complete="off"></el-input>
               </el-form-item>
            </div>
            <div class="addTeaInfoList">
               <el-form-item label="用户邮箱" prop="userEmail">
                  <el-input v-model.trim="editForm.userEmail" auto-complete="off"></el-input>
               </el-form-item>
            </div>
            <div class="addTeaInfoList">
               <el-form-item label="公司名称" prop="userCompany">
                  <el-input v-model.trim="editForm.userCompany" auto-complete="off"></el-input>
               </el-form-item>
            </div>
            <div class="addTeaInfoList">
               <el-form-item label="部门名称" prop="userDept">
                  <el-input v-model.trim="editForm.userDept" readonly auto-complete="off"></el-input>
               </el-form-item>
            </div>
            <!--<div class="addTeaInfoList">
               <el-form-item label="条线名称" prop="userLine">
                  <el-input v-model.trim="editForm.userLine" auto-complete="off"></el-input>
               </el-form-item>
            </div>-->
         </el-form>
         <div slot="footer" class="dialog-footer">
            <el-button @click.native="editFormVisible = false">取消</el-button>
            <el-button type="primary" @click.native="editSubmit" :loading="editLoading">提交</el-button>
         </div>
      </el-dialog>

      <!-- 新增界面 -->
      <el-dialog title="新增用户信息" center :visible.sync="addFormVisible" :close-on-click-modal="false">
         <el-form :model="addForm" label-width="100px" :rules="addFormRules" ref="addForm">
            <div class="addTeaInfoList">
               <el-form-item label="真实姓名" prop="userRealName">
                  <el-input v-model.trim="addForm.userRealName" auto-complete="off"></el-input>
               </el-form-item>
            </div>
            <div class="addTeaInfoList">
               <el-form-item label="用户邮箱" prop="userEmail">
                  <el-input v-model.trim="addForm.userEmail" auto-complete="off"></el-input>
               </el-form-item>
            </div>
            <div class="addTeaInfoList">
               <el-form-item label="公司名称" prop="userCompany">
                  <el-input v-model.trim="addForm.userCompany" auto-complete="off"></el-input>
               </el-form-item>
            </div>
            <div class="addTeaInfoList">
               <el-form-item label="部门名称" prop="userDept">
                  <el-input v-model.trim="addForm.userDept" readonly auto-complete="off"></el-input>
               </el-form-item>
            </div>
            <!--<div class="addTeaInfoList">
               <el-form-item label="条线名称" prop="userLine">
                  <el-input v-model.trim="addForm.userLine" auto-complete="off"></el-input>
               </el-form-item>
            </div>-->
         </el-form>
         <div slot="footer" class="dialog-footer">
            <el-button @click.native="addFormVisible = false">取消</el-button>
            <el-button type="primary" @click.native="addSubmit" :loading="addLoading">提交</el-button>
         </div>
      </el-dialog>
   </section>
</template>
<script>
   import { getUsersList, insertUser, findUserById, updateUser, deleteUser } from '../../api/api';

   export default {
      data() {
         return {
            filters: {
               name: '',
            },

            total: 0,
            page: 1,
            listLoading: false,

            editFormVisible: false, //编辑界面是否显示
            editLoading: false,
            addLoading: false,
            addFormVisible: false,

            //编辑界面数据
            editForm: {},
            addForm: { userDept: '美研' },
            users: [],
            addFormRules: {
               // userRealName: [{ required: true, message: '请输入真实姓名', trigger: 'blur' }],
               userEmail: [
                  { required: true, message: '请输入邮箱地址', trigger: 'blur' },
                  { type: 'email', message: '邮箱格式错误', trigger: 'blur' },
               ],
               // userCompany: [{ required: true, message: '请输入公司名称', trigger: 'blur' }],
               // userDept: [{ required: true, message: '请输入部门名称', trigger: 'blur' }],
               // userLine: [{ required: true, message: '请输入条线名称', trigger: 'blur' }],
               // userMobile: [
               //   { required: true, message: "请输入对接人联系方式", trigger: "blur" },
               //   { min: 11, max: 11, message: "长度11个数字", trigger: "blur" }
               // ],
            },
            pageSize: 20,
         };
      },
      methods: {
         handleCurrentChange(val) {
            this.page = val;
            this.getUsersList();
         },

         //获取用户列表
         getUsersList() {
            let params = { str: this.filters.name, pageNo: this.page, pageSize: this.pageSize };
            this.listLoading = true;
            getUsersList(params).then(res => {
               let { message, status, datas } = res;
               if (status !== 0) {
                  if (status === '' || status === undefined || status === null) {
                     this.$message.error('系统登录超时!');
                  } else {
                     this.$message.error('' + message + '');
                  }
                  this.listLoading = false;
               } else {
                  this.users = datas.list;
               }
               this.listLoading = false;
            });
         },

         //获取用户列表
         findUserById(row) {
            console.log('row', row);
            let params = { userId: row.userId };
            this.listLoading = true;
            this.editFormVisible = true;
            findUserById(params).then(res => {
               let { message, status, datas } = res;
               if (status !== 0) {
                  if (status === '' || status === undefined || status === null) {
                     this.$message.error('系统登录超时!');
                  } else {
                     this.$message.error('' + message + '');
                  }
               } else {
                  this.editForm = datas;
               }
               this.listLoading = false;
            }).catch(() => {
               console.error('emit by manager.vue of findUserById');
            });
         },

         //编辑
         editSubmit: function() {
            this.$refs.editForm.validate(valid => {
               if (valid) {
                  this.$confirm('确认提交吗?', '编辑提示', {}).then(() => {
                     this.editLoading = true;
                     delete this.editForm.createTime;
                     delete this.editForm.updataTime;
                     let para = Object.assign({}, this.editForm);
                     updateUser(para).then(res => {
                        this.editLoading = false;
                        let { message, status, datas } = res;
                        if (status !== 0) {
                           if (status === '' || status === undefined || status === null) {
                              this.$message.error('系统登录超时!');
                           } else {
                              this.$message.error('' + message + '');
                           }
                        } else {
                           // this.$refs['editForm'].resetFields();
                           this.editFormVisible = false;
                           this.getUsersList();
                        }
                     }).catch(() => {
                        console.error('emit by manager.vue of updateUser');
                     });
                  }).catch(() => {
                     this.$message.info('已取消操作!');
                  });
               }
            });
         },

         //显示新增界面
         handleAdd: function() {
            this.addFormVisible = true;
         },

         // 新增
         addSubmit: function() {
            this.$refs.addForm.validate(valid => {
               if (valid) {
                  this.$confirm('确认提交吗?', '新增提示', {}).then(() => {
                     this.addLoading = true;
                     let params = Object.assign({}, this.addForm);
                     insertUser(params).then(res => {
                        this.addLoading = false;
                        let { message, status } = res;
                        if (status !== 0) {
                           if (status === '' || status === undefined || status === null) {
                              this.$message.error('系统登录超时!');
                           } else {
                              this.$message.error('' + message + '');
                           }
                        } else {
                           this.$refs['addForm'].resetFields();
                           this.addFormVisible = false;
                           this.getUsersList();
                        }
                     }).catch(() => {
                        console.error('emit by manager.vue of insertUser');
                     });
                  }).catch(() => {
                     this.$message.info('已取消操作!');
                  });
               }
            });
         },
         // 删除
         handleDel(row) {
            console.log('row', row);
            this.$confirm('确认删除该记录吗?', '删除提示', {}).then(() => {
               this.listLoading = true;
               let param = { userId: row.userId };
               deleteUser(param).then(res => {
                  this.listLoading = false;
                  let { message, status } = res;
                  if (status !== 0) {
                     if (status === '' || status === undefined || status === null) {
                        this.$message.error('系统登录超时!');
                     } else {
                        this.$message.error('' + message + '');
                     }
                  } else {
                     this.$message.success('删除成功!');
                     this.getUsersList();
                  }
               }).catch(() => {
                  console.error('emit by manager.vue of deleteUser');
               });
            }).catch(() => {
               this.$message.info('已取消操作!');
            });
         },

         outputExcel: function() {
            let param = new URLSearchParams();
            param.append('role', 2);
            downloads(param).then(res => {
               let blob = new Blob([res], { type: 'application/vnd.ms-excel' });
               var link = document.createElement('a');
               link.href = window.URL.createObjectURL(blob);
               link.download = 'Company';
               link.click();
            });
         },
      },
      mounted() {
         this.getUsersList();
      },
   };
</script>

<style lang="scss">
   .master_table{
      .cell{
         overflow: hidden;
         text-overflow: ellipsis;
         white-space: nowrap;
      }
   }

   .addTeaInfoList{
      width: calc(50% - 5px);
      /*float: left;*/
      display: inline-block;
   }

   .el-upload__edit{
      margin-left: 10px;
      width: 150px;
      height: auto;
      line-height: 20px;
      /*margin-top: 31px;*/
      position: absolute;
      left: auto;
      top: auto;
   }
</style>
