<template>
    <div>
    <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
    <el-button type="danger" size="small">批量删除</el-button>  

    <el-table :data="address">
    <el-table-column prop="id" label="编号"></el-table-column>
    <el-table-column prop="province" label="省份"></el-table-column>    
    <el-table-column prop="city" label="城市"></el-table-column>  
    <el-table-column prop="area" label="地区"></el-table-column>  
    <el-table-column prop="address" label="地址"></el-table-column>  
    <el-table-column prop="telephone" label="联系方式"></el-table-column>  
 
    <el-table-column  label="操作">
    <template v-slot="slot">
        <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
        <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
    </template>
    </el-table-column>
    </el-table>  
    <!-- 表格结束-->
      <!-- <el-pagination
    layout="prev, pager, next"
    :total="50">
  </el-pagination> -->
  <!--横态框-->
  <el-dialog
  title="修改地址信息"
  :visible.sync="visible"
  width="60%"
  >
  {{form}}
  <el-form :model="form" label-width="80px">
    <el-form-item label="省份">
      <el-input v-model="form.province"></el-input>
    </el-form-item>
    <el-form-item label="地址">
      <el-input  v-model="form.address"></el-input>
    </el-form-item>
    <el-form-item label="手机号">
      <el-input  v-model="form.telephone"></el-input>
    </el-form-item>
    <el-form-item label="城市">
      <el-input  v-model="form.city"></el-input>
    </el-form-item>
    <el-form-item label="地区">
      <el-input  v-model="form.area"></el-input>
    </el-form-item>
    
  </el-form>
  <span slot="footer" class="dialog-footer">
    <el-button size="small" @click="closeModalHandler">取 消</el-button>
    <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
  </span>
</el-dialog>
    </div>
</template>
<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {   
    //用于存放网页中需要调用的方法
    methods:{
        loadData(){
          let url="http://localhost:6677/address/findAll"
      request.get(url).then((response)=>{
      //将直接查询结果设置到customers中
      this.address = response.data
      });
        },
        toDeleteHandler(id)
        {
        this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          //调用后台接口完成删除操作
          let url="http://localhost:6677/address/deleteById?id="+id;
          request.get(url).then((response)=>{
            //刷新数据
            this.loadData();
            //提示结果
                   this.$message({
              type: 'success',
              message :response.message
    
            });
          });
     
        })
        },

        toUpdateHandler(row){
          //在模态框表单中显示当前行的信息
          this.form=row;
            this.visible=true;

        },
        closeModalHandler(){
            this.visible=false;
        },

        toAddHandler(){
            this.visible=true;
            //将form变为初始值
            this.form={
              type:"address"

            }
        },
        submitHandler(){
          //通过request对表单的数据进行存储
          let url="http://localhost:6677/address/saveOrUpdate"
          request({
            url,
            method:"POST",
            headers:{
              "Content-Type":"application/x-www-form-urlencoded"
            },
            data:querystring.stringify(this.form)
          }).then((response)=>{
            //模态框关闭
            this.loadData();
            this.closeModalHandler();
            //提示消息
            this.$message({
              type:"success",
              message:response.message
            })
          })
        }
    },
    //用于存储要向网页中显示的数据
    data(){
        return {
            visible:false,
            address:[],
            form:{
              type:"address"
            }
    }
},
created(){
  //this为当前vue实例对象
  //vue实例创建完毕
  this.loadData();
}
}
</script>
<style scoped>

</style>
