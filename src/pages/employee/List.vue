<template>
    <div>
    <!-- 按钮 -->
    <div>
        <el-button size="samll" type="primary" @click="toAddHandler">添加</el-button>
        <el-button size="samll" type="danger">批量删除</el-button>
    </div>
     <!-- 按钮 -->
      <!-- 表格-->
      <el-table :data="employees">
          <el-table-column fixed="left" prop="id" label="编号"></el-table-column>
          <el-table-column fixed="right" prop="username" label="用户名"></el-table-column>
          <el-table-column prop="realname" label="姓名"></el-table-column>
          <el-table-column prop="gender" label="性别"></el-table-column>
          <el-table-column width="120" prop="telephone" label="手机号"></el-table-column>
          <el-table-column width="200" prop="idCard" label="身份证号"></el-table-column>
          <el-table-column width="200" prop="bankCard" label="银行卡号"></el-table-column>
          <el-table-column fixed="right" label="操作">
              <template v-slot="slot">
                  <a href="" @click.prevent="toDeleteHandler(slot.row.id )">删除</a>
                  <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>

              </template>
          </el-table-column>
      </el-table>
       <!-- 表格 -->
       <!-- 分页 -->
    <el-pagination layout="prev, pager, next" :total="50">
    </el-pagination>
       <!-- 分页 -->
        <!-- 模态框-->
        <el-dialog
        :title.sync="title" 
        :visible.sync="visible" width="60%"> 
        测试:{{form}}
        <el-form :model="form" label-width="80px">
        <el-form-item label="用户名">
          <el-input v-model="form.username"></el-input>
        </el-form-item>
        <el-form-item label="密码">
          <el-input  type="password" v-model="form.password"></el-input>
        </el-form-item>
        <el-form-item label="姓名">
          <el-input   v-model="form.realname"></el-input>
        </el-form-item>
        <el-form-item label="性别">
            <el-radio-group v-model="form.gender">
                <el-radio label="男">男</el-radio>
                <el-radio label="女">女</el-radio>
            </el-radio-group>
        </el-form-item>
        <el-form-item label="电话号码">
          <el-input   v-model="form.telephone"></el-input>
        </el-form-item>
        <el-form-item label="身份证号">
          <el-input   v-model="form.idCard"></el-input>
        </el-form-item>
        <el-form-item label="银行卡号">
          <el-input   v-model="form.bankCard"></el-input>
        </el-form-item>               
        </el-form>
        <span slot="footer" class="dialog-footer">
        <el-button size="small" @click="closeModalHandler">取 消</el-button>
        <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
        </span>
        </el-dialog>
         <!--模态框-->
    </div>
</template>
<script>
import request from '@/utils/request'   //自定义
import querystring from 'querystring'  //系统库
export default { 
    data(){
        return {
            title:"录入员工信息",
            visible:false,
            employees:[],
            form:{
                type:"waiter"
            }

        }
    },
    created(){
        //在页面加载出来的时候加载数据
        this.loadData();
    },
    methods: {
        submitHandler(){
            let url = "http://localhost:6677/waiter/saveOrUpdate";
            //前向后台发送请求，完成数据的保存操作
            request({
                url,
                method:"post",
                //告诉给后台有我的请求体中放的使查询字符串
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.form)
            }).then((response)=>{
                this.closeModalHandler();
                this.loadData();
                this.$message({type:"success",message:response.message})
            })
        },
        //重载
        loadData(){
            //调用request来接后台  ，类似使者，送信息
            let url = "http://localhost:6677/waiter/findAll"
            //写箭头原因 message方法有this这个值  指向vue实例 访问该实例属性  methods
            //中其它方法
            request.get(url).then((response)=>{     
                this.employees=response.data;
            })
        },
        closeModalHandler(){
            this.visible=false;
        },
        toAddHandler(){
            this.title="录入员工信息";
            this.visible=true;
        },
        toUpdateHandler(id){
            this.title="修改员工信息";
            this.visible=true;
        },
        toDeleteHandler(id){
        //确认
            this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
            //调用后台接口完成参数
            let url="http://localhost:6677/waiter/deleteById?id="+id;
            request.get(url).then((response)=>{
                this.loadData();
                    this.$message({
                    type: 'success',
                    message: response.message
                });
            })
          
        })
        }


    } 
 
}
</script>
<style scoped>

</style>
