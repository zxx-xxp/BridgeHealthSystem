<template>
  <div>


    <!-- ------------------------------------------------------------------------------ -->
    <!-- 用户列表主体 -->

    <!-- 搜索区域 -->
    <!-- layout布局  通过基础的 24 分栏，迅速简便地创建布局 -->
    <el-card>
      <el-row :gutter="25">
        <!-- row控制一行分布  gutter 属性来指定每一栏之间的间隔25 -->
        <el-col :span="10">
          <!-- 搜索区域 -->
          <!-- 搜索添加 -->
          <!--  clearable @clear="getUserList"清空数据后 重新查询数据 -->
          <el-input placeholder="请输入搜索的用户名信息" v-model="queryInfo.query" clearable @clear="getUserList">
            <!-- 使用clearable属性即可得到一个可清空的输入框 -->
            <!-- 下面这个按钮@click 点击按钮触发模糊查询 -->
            <el-button slot="append" icon="el-icon-search" @click="getUserList"></el-button>
          </el-input>
        </el-col>
        <el-col :span="4">
          <!-- 搜索按钮 -->
          <el-button type="primary" @click="addDialogVisible = true">添加用户</el-button>
        </el-col>
      </el-row>
      <!-- ------------------------------------------------------------------------------ -->
      <!-- 用户表区域 -->
      <!-- 用户列表 border stripe 隔行变色-->
      <el-table :data="userList" border stripe style="margin-top:20px;margin-bottom:20px">
        <!-- 索引列 -->
        <el-table-column type="index"></el-table-column>
        <!-- label内容描述 -->
        <el-table-column label="用户名" prop="username"></el-table-column>
        <el-table-column label="邮箱" prop="email"></el-table-column>
        <el-table-column label="密码" prop="password"></el-table-column>
        <el-table-column label="角色" prop="role"></el-table-column>
        <el-table-column label="状态" prop="state">
          <!-- 作用域插槽 -->
          <template slot-scope="scope">
            <!-- scope.row 放每一行的数据  el-switch switch开关
            value / v-model	绑定值	boolean / string / number-->
            <el-switch v-model="scope.row.state"  @change="userStateChanged(scope.row)"></el-switch>
          </template>
        </el-table-column>
        <el-table-column label="操作">
          <!-- 按钮 -->
          <template slot-scope="scope">
            <!-- 修改 -->
            <el-button type="primary" icon="el-icon-edit" circle size="mini" @click="showEditDialog(scope.row.id)"></el-button>
            <!-- 删除 -->
            <el-button type="danger" icon="el-icon-delete" circle size="mini" @click="deleteUser(scope.row.id)"></el-button>
            <!-- 权限 -->
            <!-- 文字提醒 -->
            <!-- 使用content属性来决定hover时的提示信息。
            由placement属性决定展示效果：placement属性值为：方向-对齐位置  
            enterable鼠标是否可以进入到tooltip里面 -->
            <el-tooltip class="item" effect="dark" content="分配权限" placement="top-start"  :enterable="false">
            <el-button type="warning" icon="el-icon-star-off" circle size="mini"></el-button>
            </el-tooltip>
          </template>
        </el-table-column>
      </el-table>

      <!-- <span>{{userList}}</span> -->
      <!-- 分页 -->
      <!-- size-change和current-change事件来处理
      页码大小和当前页变动时候触发的事件
      page-sizes数组元素  每页显示个数的选项
      [100, 200, 300, 400]表示四个选项，
      每页显示 100 个，200 个，300 个或者 400 个。 -->
      <el-pagination
      @size-change="handleSizeChange"
      @current-change="handleCurrentChange"
      :current-page="queryInfo.pageNum"
      :page-sizes="[1,2,5,100]"
      :page-size="queryInfo.pageSize"
      layout="total, sizes, prev, pager, next, jumper"
      :total="total">
    </el-pagination>
    </el-card>

    <!-- 新增用户区域   @close="addDialogClosed"关闭弹窗触发方法
    element-ui 中的dialog组件时，发现visible属性在使用时需要添加.sync才生效。
    :visible.sync="addDialogVisible"控制关闭弹窗-->
    <el-dialog title="添加用户" :visible.sync="addDialogVisible" width="50%"  >
      <!-- ref属性可以在 methods 里进行指定的表单进行认证 -->
      <el-form :model="addForm" :rules="addFormRules" ref="addFormRef" label-width="70px">
        <!-- 用户名 -->
        <el-form-item label="用户名" prop="username">
          <el-input v-model="addForm.username"></el-input>
        </el-form-item>
          <!-- 密码 -->
          <el-form-item label="密码" prop="password">
          <el-input v-model="addForm.password"></el-input>
        </el-form-item>
          <!-- 邮箱 -->
          <el-form-item label="邮箱" prop="email">
          <el-input v-model="addForm.email"></el-input>
         </el-form-item>
      </el-form>
      <!-- 内容底部两个按钮 -->
      <span slot="footer" class="dialog-footer">
        <el-button @click="addDialogVisible=false">取消</el-button>
        <el-button type="primary" @click="addUser">确定</el-button>
      </span>
    </el-dialog>
    <!-- 修改对话框 -->
       <el-dialog title="修改用户信息" :visible.sync="editDialogVisible" width="50%" @close="editDialogClosed" >
      <!-- ref属性可以在 methods 里进行指定的表单进行认证 -->
      <el-form :model="editForm" :rules="editFormRules" ref="editFormRef" label-width="70px">
        <!-- 用户名 -->
        <el-form-item label="用户名" prop="username">
          <!-- disable不能修改 用户名只作为展示用 -->
          <el-input v-model="editForm.username" disabled></el-input>
        </el-form-item>
          <!-- 密码 -->
          <el-form-item label="密码" prop="password">
          <el-input v-model="editForm.password"></el-input>
        </el-form-item>
          <!-- 邮箱 -->
          <el-form-item label="邮箱" prop="email">
          <el-input v-model="editForm.email"></el-input>
         </el-form-item>
      </el-form>
      <!-- 内容底部两个按钮 -->
      <span slot="footer" class="dialog-footer">
        <el-button @click="editDialogVisible=false">取消</el-button>
        <el-button type="primary" @click="editUserInfo">确定</el-button>
      </span>
    </el-dialog>

  </div>
</template>

<script>
export default {
  created() {
    // 一进来就调用getUserList方法
    this.getUserList();
    // this.test();
  },
  data() {
    return {
      // 查询信息queryInfo
      queryInfo: {
        query: "", //查询信息
        pageNum: 1, //当前页
        pageSize: 5, //每页最大数
      },
      userList: [{
        username:"zxx",
        password:123456,
        email:"295641679@qq.com",
        state:0,
        role:"管理员"
      },
      {
         username:"zpx",
        password:123456,
        email:"295641679@qq.com",
        state:1,
        role:"管理员"
      }], // 用户列表
      total: 0, //初始最大数
      addDialogVisible:false,//添加的对话框状态
       // 显示或隐藏修改弹窗
      editDialogVisible:false,
      //添加表单信息
      addForm: {
        username: "",
        password: "",
        email:""
      },
      // 修改用户的信息，然后查询该id的用户信息，把用户信息存储到editForm
      editForm:{ },
     
      // 修改的表单验证
         editFormRules: {
        // 校验密码
        password:[
            { required: true, message: '请输入用户名所对应的密码', trigger: 'blur' },//必填项验证
            { min: 6, max: 12, message: '长度在 6到 12 个字符', trigger: 'blur' }
        ],
        // 邮箱验证
          email:[
            { required: true, message: '请输入邮箱', trigger: 'blur' },//必填项验证
            { min: 5, max: 15, message: '请输入正确的邮箱', trigger: 'blur' }
        ]
      },
      //添加的表单验证
      addFormRules: {
        // 校验用户名 
        // 设置的名称要和loginForm中的对应
         username: [
          { required: true, message: "请输入用户名", trigger: "blur" },//必填项验证
          { min: 5, max: 12, message: "长度在 5 到 12 个字符", trigger: "blur" },
        ],
        // 校验密码
        password:[
            { required: true, message: '请输入用户名所对应的密码', trigger: 'blur' },//必填项验证
            { min: 6, max: 12, message: '长度在 6到 12 个字符', trigger: 'blur' }
        ],
        // 邮箱验证
          email:[
            { required: true, message: '请输入邮箱', trigger: 'blur' },//必填项验证
            { min: 5, max: 15, message: '请输入正确的邮箱', trigger: 'blur' }
        ]
      },

    };
  },
  methods: {

    // 获取所有用户
    async getUserList() {
      // 测试
      // console.log(123);
      // const { data: res } = await this.$http.get("alluser", {
      //   params: this.queryInfo,
      // });
      console.log(res);
      res=[{
        username:"zx",
        password:123456,
        email:"295641679@qq.com",
        state:0,
        role:"管理员"
      },
      {
         username:"px",
        password:123456,
        email:"295641679@qq.com",
        state:1,
        role:"管理员"
      }]
      this.userList = res.data;//用户列表数据封装
      this.total = 2;//总用户数
    },
    // 以下两个方法处理----页码大小和当前页变动时候触发的事件

    // 最大数 点击每页最大数
    handleSizeChange(newSize){
      // 改变每页最大数，重新传入后端，后端实现展示
      this.queryInfo.pageSize = newSize;
      // 传入当前的userList
      this.getUserList();
    },

    //pageNum的触发动作 点击页码
    handleCurrentChange(newPage){
      this.queryInfo.pageNum = newPage;
      this.getUserList();
    },

    async userStateChanged(userInfo){
      const {data :res} = await this.$http.post(`userstate?id=${userInfo.id}&state=${userInfo.state}`);
      if(res!="success"){
        // ??
        userInfo.id=!userInfo.id;
        return this.$message.error("操作失败，无效id");
      }
      this.$message.success("操作成功");
    },

    addDialogClosed(){
      this.$refs.addFormRef.resetFields();
    },
    addUser(){
      // 点击弹窗后 addDialogVisible会变为true
      // console.log("tanchuang",this.addDialogVisible);
      // 根据表单规则检验 符合则vaild==true
      this.$refs.addFormRef.validate(async vaild=>{
        // console.log("vaild");
        // console.log(vaild);
        if(!vaild) return;
        console.log("addUser:",this.addForm);
        // const {data:res}=await this.$http.post("addUser",this.addForm);
        res="success"
        if(res!="success"){
          return this.$message.error("添加失败");
        }
        this.$message.success("添加成功");
        this.addDialogVisible = false;
        this.getUserList();
      })
    },
    async deleteUser(id){
      // 这个id是查询信息后得到的数据库实际的id
      // console.log(id);
      // confirmResult放到结果里面，值为确定或者取消
    const confirmResult = await this.$confirm('此操作将永久删除用户，是否继续?','提示',{
        confirmButtonText:'确定',
        cancelButtonText:'取消',
        type:'warning',
      }).catch(err => err)
      if(confirmResult!='confirm'){
        return this.$message.info("已取消删除");
      }
       res="success"
    //  const {data:res} = await this.$http.delete("deleteUser?id="+id);
     if(res!="success"){
       return this.$message.error("删除失败");
     }
     this.$message.success("删除成功");
     this.getUserList();
    },
    // 显示对话框
   async showEditDialog(id){
    //  查询用户信息反填回去
     const {data:res} = await this.$http.get("getUpdate?id="+id);
     this.editForm = res;
     this.editDialogVisible=true;//开启编辑对话框
   },
  //  关闭对话框
  editDialogClosed(){
    this.$refs.editFormRef.resetFields();//重置信息
  },
  // 确认修改
  editUserInfo(){
    // console.log("112");
    this.$refs.editFormRef.validate(async valid=>{
      if(!valid) return;
      // 发起修改请求
      const{data:res}= await this.$http.put("editUser",this.editForm);
      // console.log("2222");
      if(res!="success"){
       return this.$message.error("修改失败");
     }
     this.$message.success("修改成功");
     this.editDialogVisible=false;
     this.getUserList();
    })
  },


  },
};
</script>
<style >
.el-breadcrumb {
  margin-bottom: 15px;
  font-size: 17px;
}
</style>