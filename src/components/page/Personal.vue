<template>
    <div>
        <el-form :model="ruleForm" :rules="rules" ref="ruleForm" size="medium" label-width="100px" class="demo-ruleForm">
  <el-form-item label="姓名" prop="name">
    <el-input v-model="ruleForm.name"></el-input>
  </el-form-item>
  <el-form-item label="密码" prop="password">
         <el-input type="password" v-model="ruleForm.password" placeholder="请输入密码"></el-input>
</el-form-item>
  <el-form-item label="确认密码" prop="confirmPassword">
       <el-input type="password" v-model="ruleForm.confirmPassword" placeholder="请确认密码"></el-input>
 </el-form-item>
 <el-form-item label="邮箱" prop="email">
         <el-input v-model="ruleForm.email" placeholder="请输入邮箱"></el-input>
   </el-form-item>
 <el-form-item label="手机号" prop="mobile">
         <el-input  v-model="ruleForm.mobile" placeholder="请输入手机号"></el-input>
</el-form-item>
  <el-form-item label="身份" prop="region">
    <el-select v-model="ruleForm.region" placeholder="请选择活动区域">
      <el-option label="巡检人员" value="shanghai"></el-option>
      <el-option label="维修人员" value="beijing"></el-option>
    </el-select>
  </el-form-item>
  <el-form-item label="活动时间" required>
    <el-col :span="11">
      <el-form-item prop="date1">
        <el-date-picker type="date" placeholder="选择日期" v-model="ruleForm.date1" style="width: 200px;"></el-date-picker>
      </el-form-item>
    </el-col>
    <el-col class="line" :span="2">-</el-col>
    <el-col :span="11">
      <el-form-item prop="date2">
        <el-time-picker placeholder="选择时间" v-model="ruleForm.date2" style="width:200px;"></el-time-picker>
      </el-form-item>
    </el-col>
  </el-form-item>
  <el-form-item label="状态" prop="delivery">
    <el-switch v-model="ruleForm.delivery"></el-switch>
  </el-form-item>
  <el-form-item label="活动性质" prop="type">
    <el-checkbox-group v-model="ruleForm.type">
      <el-checkbox label="美食/餐厅线上活动" name="type"></el-checkbox>
      <el-checkbox label="地推活动" name="type"></el-checkbox>
      <el-checkbox label="线下主题活动" name="type"></el-checkbox>
      <el-checkbox label="单纯品牌曝光" name="type"></el-checkbox>
    </el-checkbox-group>
  </el-form-item>
  <el-form-item label="特殊资源" prop="resource">
    <el-radio-group v-model="ruleForm.resource">
      <el-radio label="线上品牌商赞助"></el-radio>
      <el-radio label="线下场地免费"></el-radio>
    </el-radio-group>
  </el-form-item>
  <el-form-item label="活动形式" prop="desc">
    <el-input type="textarea" v-model="ruleForm.desc"></el-input>
  </el-form-item>
  <el-form-item>
    <el-button type="primary" @click="submitForm('ruleForm')">立即创建</el-button>
    <el-button @click="resetForm('ruleForm')">重置</el-button>
  </el-form-item>
</el-form>

    </div>
</template>

<script>
  export default {
      
    data() {
         var validPhone=(rule, value, callback)=>{
            if(value){
                if(!(/^1[3456789]\d{9}$/.test(value))){
                    callback(new Error('手机号格式不正确'));
                }else{
                    callback();
                }
            }else{
                callback();
            }
        };
          var validConfirmPassword=(rule, value, callback)=>{
            if(value){
                if(value != this.ruleForm.password){
                    callback(new Error('确认密码和密码不一致'));
                }else{
                    callback();
                }
            }else{
                callback();
            }
        }
      return {
        ruleForm: {
          email:"",//团体账号 电子邮箱
          mobile:"",//手机号
          name: '',
          password:'',
          region: '',
          date1: '',
          date2: '',
          delivery: false,
          type: [],
          resource: '',
          desc: '',
          confirmPassword:'',
          
        },
        rules: {
              email:[
                    { required: true, message: '请填写邮箱', trigger: 'change' },
                    { type: 'email', message: '请输入正确的邮箱地址', trigger: ['blur', 'change'] }
                ],
                 mobile:[
                    { required: true, message: '请输入手机号', trigger: 'change' },
                    { validator: validPhone, trigger: 'change' }
                ],
          name: [
            { required: true, message: '请输入活动名称', trigger: 'blur' },
            { min: 3, max: 5, message: '长度在 3 到 5 个字符', trigger: 'blur' }
          ],
           password:[
            { required: true, message: '请输入用户名所对应的密码', trigger: 'blur' },//必填项验证
            { min: 6, max: 12, message: '长度在 6到 12 个字符', trigger: 'blur' }
        ],
        confirmPassword:[
                    { required: true, message: '请确认密码', trigger: 'change' },
                    { validator: validConfirmPassword, trigger: 'change' },
                ],
          region: [
            { required: true, message: '请选择活动区域', trigger: 'change' }
          ],
          date1: [
            { type: 'date', required: true, message: '请选择日期', trigger: 'change' }
          ],
          date2: [
            { type: 'date', required: true, message: '请选择时间', trigger: 'change' }
          ],
          type: [
            { type: 'array', required: true, message: '请至少选择一个活动性质', trigger: 'change' }
          ],
          resource: [
            { required: true, message: '请选择活动资源', trigger: 'change' }
          ],
          desc: [
            { required: true, message: '请填写活动形式', trigger: 'blur' }
          ]
        }
      };
    },
    methods: {
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
      resetForm(formName) {
        this.$refs[formName].resetFields();
      }
    }
  }
</script>
<style  >
.el-form-item {
    width:600px;
}
</style>