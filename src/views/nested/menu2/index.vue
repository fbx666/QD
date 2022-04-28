<template>
  <div style="padding:30px;text-align: center">
<!--    <el-alert :closable="false" title="menu 2" />-->
    <el-form :model="ruleForm" status-icon :rules="rules" ref="ruleForm" label-width="100px" class="demo-ruleForm">
      <el-form-item label="账号" prop="name">
        <el-input type="name" v-model="ruleForm.name" autocomplete="off"></el-input>
      </el-form-item>
      <el-form-item label="密码" prop="checkPass">
        <el-input type="password" v-model="ruleForm.password" autocomplete="off"></el-input>
      </el-form-item>
      <el-button @click="register">注册</el-button>
    </el-form>

<!--    <el-button @click="getData">{{this.result}}</el-button>-->
  </div>
</template>

<script>
import { mapGetters } from 'vuex'
import axios from 'axios';
export default {
  data() {
    return {
      result:"答案不对！",
      name:"null",
      password:"null",
      //以下为表单
      ruleForm: {
        name: '',
        password: ''
      },
    }
  },
  methods: {
    // 第一个方法解决表单无法输入的情况
    change(e){
      this.$forceUpdate()
    },
    getData() {
      const path = 'http://127.0.0.1:5000/test';
      axios.post(path,
                {alg:'huhuhu'},
        ).then(res => {
        var msg = res.data.msg;
        this.result = msg; // 因为不能直接使用this作为指针，因此在这之前将this赋给了then指针
        this.$message({message:'操作成功！',type:'success',showClose:false});
        // alter('Success' + response.status + ',' + response.data + ',' + msg); // 成功后显示提示
      }).catch(error => {
        this.$message({message:'操作失败！',type:'error',showClose:false});
        console.error(error);
      });
    },
    register() {
      const path = 'http://127.0.0.1:5000/register';
      axios.post(path,
        {name:this.ruleForm.name,
         password:this.ruleForm.password},
      ).then(res => {
        var msg = res.data.msg;
        this.result = msg; // 因为不能直接使用this作为指针，因此在这之前将this赋给了then指针
        this.$message({message:'操作成功！',type:'success',showClose:false});
        // alter('Success' + response.status + ',' + response.data + ',' + msg); // 成功后显示提示
      }).catch(error => {
        this.$message({message:'操作失败！',type:'error',showClose:false});
        console.error(error);
      });
    }

  }
}

</script>
