# it-项目管理
 <el-main>
              
              <div class="login_container">
                <el-card>
                      <div class="login-title">账户密码登录</div>
                      
                      <el-form ref="loginForm" :model="loginForm" class="login-form" :rules="loginRules"   >
                        <!-- 双向绑定  loginForm 用户名  -->
                        <el-form-item prop="username">
                          <!-- 我们学的第一个vue指令 叫做  v-model 双向绑定  v-bind 单向绑定 -->
                            <el-input v-model="loginForm.username" placeholder="请输入用户名称"></el-input>
                            <!-- 大胡子表达式  -->
                        </el-form-item>
                        <!-- 密码 -->
                        <el-form-item  prop="password" >
                            <el-input type="password" v-model="loginForm.password" placeholder="请输入用户密码"  ></el-input>
                        </el-form-item>
                        <el-form-item  prop="type">
                          <el-select v-model="loginForm.type" placeholder="请选择">
                            <el-option v-for="item in options" 
                            :key="item.value" :label="item.label" :value="item.value" :disabled="item.disabled"> 
                            </el-option> 
                          </el-select>
                        </el-form-item>
                        <!-- 点击 @click html onclick  -->
                        <el-button type="warning"  style="width:100%" @click="login" >
                            登录
                        </el-button>
                        <el-tooltip class="item" effect="dark" content="请先阅读并勾选协议" placement="top-start" :value="checked===a" manual>
                          <el-checkbox v-model="checked" @change="ty"></el-checkbox>
                        </el-tooltip>
                        <span class="p">阅读并同意携程的 <a href="#">服务协议</a>和<a href="#">个人信息保护政策</a> </span>
                        <div style="display: flex; justify-content: space-between;">
                            <!-- <a href="#">忘记密码</a> -->
                            <a href="/reg">免费注册</a>
                        </div>
                      </el-form>
                </el-card>
              </div>
            </el-main>

 methods:{
    ty(){
        this.a=!this.checked
    },
    // 登录方法
    login(){
      this.$refs['loginForm'].validate((valid)=>{
        if(valid){
              // 判断是不是看了信任协议
              // !false=true 
              if(!this.checked){
                this.a=false;
                return ;
              }
              this.$http.get("/user/login",{params:this.loginForm}).then(res=>{
                if(res.data.code==200){
                  localStorage.setItem("user",JSON.stringify(res.data.data))
                  if(res.data.data.type==0){
                      this.$router.push("/home");
                  }else{
                    this.$router.push("/layout");
                  }
                    this.$message.success("登录成功");
                }else{
                  this.$message.error("登录失败");
                }
              })
        }
      })
    }
  }


<script>
  export default {
      name: "",
      data(){
          /* 定义初始化变量 */
          return{
            options:[
              {
                value:0,
                label:'客户'
              },
              {
                value:1,
                label:'管理员'
              },
            ],
            active:1,
            // 用户名  密码  确认密码 type 管理员 还是非管理员 0 普通 1 管理员
            form:{
              username:'',
              password:'',
              confirmPassword:'',
              nickname:'',
              phone:'',
              type:''
            },
            rules:{
              username: [
                { required: true, message: '请输入用户名', trigger: 'blur' },
              ],
              password: [
                { required: true, message: '请输入密码', trigger: 'blur' },
              ],
              confirmPassword: [
                { required: true, message: '请输入确认密码', trigger: 'blur' },
              ],
              nickname: [
                { required: true, message: '请输入姓名', trigger: 'blur' },
              ],
                 phone: [
                { required: true, message: '请输入联系方式', trigger: 'blur' },
              ],
            }
          }
      },
      /* 定义事件函数 */
      methods:{
        // 注册方法
        reg(){
          this.$refs.form.validate((valid=>{
              if(valid){
                // 判断这个密码是不是相等 toLowerCase  将字符串转换为小写
                if(this.form.password.toLowerCase()===this.form.confirmPassword.toLowerCase()){
                  this.$http.post('/user/add',this.form).then(res=>{
                    if(res.data.code==200){
                        this.$message.success("注册成功");
                        this.active++;
                        return;
                    }
                    this.$message.error("注册失败");
                })
                }else{
                  this.$message.error("两次密码不一致 请确认输入后的密码");
                }
              }
          }))
        },
        // 输入密码
        nextPwd(){
              if(this.form.username.trim()===""){
                this.$message.error("请输入用户名");
                return ;
              }
              // 判断如果这个用户名已经存在 
              this.$http.get("/user/selectByUesrName?username="+this.form.username).then(res=>{
                if(res.data.code==200){
                  this.active++;
                  return ;
                }
                this.$message.error(res.data.msg);
              })
              // 将进度条 往后走一步  
              // i++ ++i
        }
      },
  }
</script>


<div class="table">
      <el-card class="zhu">
          <el-form :model="searchForm" ref="mysearchForm" 
          class="top" :inline="true">
                  <el-form-item prop="username">
                      <el-input v-model="searchForm.username"
                      placeholder="请输入账号" size="mini"></el-input>
                  </el-form-item>
      
                  <el-form-item>
                      <el-button type="primary" @click="fetchData"
                      round size="mini">查询</el-button>
                      <el-button @click="searchReset('mysearchForm')"
                      round size="mini">重置</el-button>
                  </el-form-item>
          </el-form>
          <el-table class="middle" :data="list" border
          highlight-current-row max-height="600px">
              <el-table-column type="index" label="序号"
              align="center"></el-table-column>
              <el-table-column prop="username" label="账号"
              align="center"></el-table-column>
              <el-table-column prop="nickname" label="姓名"
              align="center"></el-table-column>
              <el-table-column prop="phone" label="电话"
              align="center"></el-table-column>
              <el-table-column prop="gender" label="性别"
              align="center">
              <template slot-scope="scope">
                      {{ scope.row.gender == 1 ? "男" :  "女" }}
                  </template>
          
      <el-dialog title="修改信息" :visible.sync="dialogFormVisible"
      width="400px">
          <el-form :model="updateForm" :rules="updateRules"
          ref="myUpdateFrom" label-width="auto">
          <el-form-item label="账号" prop="username">
                          <!-- 我们学的第一个vue指令 叫做  v-model 双向绑定  v-bind 单向绑定 -->
                            <el-input v-model="updateForm.username" placeholder="请输入用户名称"></el-input>
                            <!-- 大胡子表达式  -->
           </el-form-item>
           <el-form-item  label="姓名" prop="nickname">
                            <el-input v-model="updateForm.nickname" placeholder="请输入用户名称"></el-input>
           </el-form-item>
           <el-form-item label="联系电话" prop="phone">
                            <el-input v-model="updateForm.phone" placeholder="请输入用户电话"></el-input>
           </el-form-item>
           <el-form-item label="角色" prop="type">
                  <el-select v-model="updateForm.type" placeholder="请选择">
                     <el-option v-for="item in options" 
                     :key="item.value" :label="item.label" :value="item.value" :disabled="item.disabled"> 
                    </el-option> 
                  </el-select>
                </el-form-item>
          </el-form>
          <div slot="footer" class="dialog-footer">
              <el-button @click="dialogFormVisible = false">取 消</el-button>
              <el-button type="primary" @click="update">修 改</el-button>
          </div>
      </el-dialog>
methods: {
          fetchData(){
            this.$http.get('/user/selectUser',{params:this.searchForm}).then(res=>{
                    if(res.data.code === 200){
                      this.list = res.data.data.rows
                      this.total = res.data.data.total
                 }
            })
          },
          del(id){
              this.$confirm('你确定删除本条记录吗？','提示',{
                  confirmTextButton:'确定',
                  cancelTextButton:'取消',
              }).then(()=>{
                this.$http.get("/user/deleteById?id="+id).then(res=>{
                    if(res.data.code == 200 ){
                        this.$message.success("删除成功");
                        this.fetchData()
                    }
                })
              }).catch(()=>{
                  this.$message.success("取消成功");
                        this.fetchData()
              })
          },
          handleUpdate(one){
              this.dialogFormVisible = true
              this.$nextTick(()=>{
                  this.$refs['myUpdateFrom'].resetFields();
                  this.updateForm = {...one}
              })
          },
          update(){
              this.$refs['myUpdateFrom'].validate(valid =>{
                  if(valid){
                      this.$http.post("/user/update",this.updateForm).then(resp=>{
                            if(resp.data.code==200){
                                this.$message.success("编辑成功");
                                this.dialogFormVisible = false;
                                this.fetchData();
                                return;
                            }
                        })
                  }
              })
          },
          handleSizeChange(val){
              this.itemsPerPage = val
              this.fetchData()
          },
          handleCurrentChange(val){
              this.page = val
              this.fetchData()
          },
          searchReset(formName){
            this.searchForm.username='';
              this.$refs[formName].resetFields()
              this.fetchData()
          },
          changeRole(id,role){
              this.updateRole.id = id
              this.updateRole.role = role
              // dao.update(this.updateRole).then(res =>{
              //     this.$message({
              //         type:res.data.flag?'success':'error',
              //         message:res.data.msg,
              //     })
              // })
          },
      }


            style="margin: 0 5px"
            :key="tag"
            v-for="tag in form.tagArr"
            closable
            :disable-transitions="false"
            @close="handleClose(tag)">
          {{tag}}
        </el-tag>
        <el-input
            v-model="inputValue"
            ref="saveTagInput"
            size="small"
            @keyup.enter.native="handleInputConfirm"

        ></el-input>
      </el-form-item>
      <el-form-item label="封面：" prop="pic">
        <el-upload
            ref="cover"
            :action=" 'http://localhost:9000/file/upload' "
            list-type="picture-card"
            :on-success="coverSuccess"
            :on-remove="coverRemove"
            :on-exceed="coverExceed"
            :limit="1">
          <i class="el-icon-plus"></i>
        </el-upload>
      </el-form-item>
      <el-form-item label="退房要求：" prop="introduce">
        <el-input  type="textarea" v-model="form.introduce"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="submitForm('form')">立即添加</el-button>
        <el-button @click="resetForm('form')">重置</el-button>
      </el-form-item>
    </el-form>

methods: {
    coverSuccess(res,file,fileList){
      this.form.pic = res.data
    },
    coverRemove(file,fileList){
      this.form.pic = ''
    },
    coverExceed(){
      this.$message.error('封面只能上传一张！')
    },
    handleClose(tag) {
      this.form.tagArr.splice(this.form.tagArr.indexOf(tag), 1);
    },
    handleInputConfirm() {
      let inputValue = this.inputValue;
      if (inputValue) {
        console.log(this.form.tagArr)
        console.log(inputValue)
        if(this.form.tagArr.indexOf(inputValue)>=0) {
          this.$message.error("该标签已添加！")
          return
        }
        this.form.tagArr.push(inputValue);
        this.inputValue = '';
      }
    },
    submitForm(formName) {
      if( this.form.addr&& this.district.length>0){
        this.form.address = "中国，" + this.district.join("，") +  "，"+ this.form.addr
      }
      if(this.form.tagArr.length>0){
        this.form.tag=this.form.tagArr.join(",")
      }
      this.$refs[formName].validate((valid) => {
        if (valid) {
          this.$http.post("/homestay/add",this.form).then(resp=>{
            if(resp.data.code==200){
                this.$message.success("添加成功");
                this.resetForm('form');
                return;
            }
          })
        } else {
          console.log('error submit!!');
          return false;
        }
      });
    },
    resetForm(formName) {
      this.$refs[formName].resetFields();
    }
  },
  created() {
    let user = JSON.parse(localStorage.getItem("user")||'{}')
    if(user.id){
      this.form.uid = user.id
    }
  }

 <el-main>
        <div class="main">
            <el-card>
              <div slot="header" >
                <span>全部订单</span>
              </div>
              <div v-if="orders.length">
                <el-card class="order-item" v-for="o in orders" :key="o.id" >
                  <div slot="header" class="clearfix">
                    <span>订单号：{{o.id }} &nbsp; 预订日期：{{o.createTime}}&nbsp;  
                      <el-button type="text" @click="delOrder(o.id)">退房</el-button></span>
                      <el-button type="danger" plain size="small" style="margin-left: 10px;" @click="startRepair(o.id)">报修</el-button>
                  </div>
                  <div class="order-details">
                    <div class="left">
                      <h3>{{ o.hname }}</h3>
                      <p>{{ o.address }}</p>
                      <p>入住日期: {{ o.checkInTime }}  1晚/1间</p>
                      <p>入住人：{{ o.name }}</p>
                      <p>{{ o.roomTypeName }}</p>
                    </div>
                    <div class="right">
                      <h4 style="color:rgb(170, 170, 170)"  v-if="o.status==3">已取消</h4>
                      <h4 style="color:#67C23A" v-else-if="o.status==2">已完成</h4>
                      <h4  style="color:#409EFF" v-else-if="o.status==1" >已预定</h4>
                      <div style="font-size: 12px">到店付 <span style="font-weight: 550;font-size: 18px">￥{{o.rooms}}</span></div>
                      <el-button v-if="o.status==1" style="margin-top: 30px" type="text" @click="cancelOrder(o.onum)"> 取消订单</el-button>      
                    </div>
                  </div>
                </el-card>
queryAll(){
       this.$http.get('/order/selectByUid',{params:this.params}).then(res=>{
        this.orders = res.data.data.rows
         this.total = res.data.data.total
       })
    },
    cancelOrder(onum){
      this.$confirm('是否确定取消该订单?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '放弃',
        type: 'warning'
      }).then(() => {
        this.$http.put('/order/cancelOrder?onum='+onum ).then(res=>{
          if(res.data.code==200){
            this.$message.success("取消成功~")
            this.queryAll()
          }else{
            this.$message.success("取消失败，请稍后重试！")
          }
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已放弃取消订单'
        });
      });
    },
    delOrder(onum){
      this.$confirm('此操作将取消该订单, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '放弃',
        type: 'warning'
      }).then(() => {
        this.$http.delete('/order?onum='+onum ).then(res=>{
          if(res.data.code==200){
            this.$message.success("取消成功~")
            this.queryAll()
          }else{
            this.$message.success("取消失败，请稍后重试！")
          }
        })
      }).catch(() => {
        this.$message({
          type: 'info',
          message: '已取消订单'
        });
      });
    }
  },
  created() {
    let user = JSON.parse(localStorage.getItem("user")||'{}')
    if(user.id){
      this.user = user
      if(this.user.type==1){
        this.queryAll()
      }else{
        this.params.uid= user.id
        this.queryAll()
      }
    }
  }

      <el-dialog  :visible.sync="dialogFormVisible">
        <el-form :model="aTable">
          <el-form-item label=" 故障描述" label-width="120">
            <el-input v-model="aTable.pic" autocomplete="off"></el-input>
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button @click="dialogFormVisible = false">取 消</el-button>
          <el-button type="primary" @click="repair">确 定</el-button>
        </div>
      </el-dialog>      
 

<el-form :inline="true" :model="params" class="demo-form-inline">
          <el-form-item label="报修服务">
            <el-input v-model="params.str" placeholder="请输入订单号"></el-input>
          </el-form-item>
          <el-form-item>
            <el-button type="primary" @click="query">查询</el-button>
            <el-button type="primary" @click="reset" >重置</el-button>
            <el-button type="primary" @click="addShow" >添加</el-button>
          </el-form-item>
        </el-form>
        <el-table
            :data="data"
            stripe
            style="width: 100%">
          <el-table-column
              type="index"
              label="序号"
              width="150"
          >
          </el-table-column>
          <el-table-column
              prop="str"
              label="订单号"
              width="150"
          >
          </el-table-column>
          <el-table-column
              prop="num"
              label="住户姓名"
          >
          </el-table-column>
          <el-table-column
              prop="price"
              label="联系电话"
          >
          </el-table-column>
          <el-table-column
              prop="pic"
              label="报修描述"
              width="150"
          >
          </el-table-column>
          <el-table-column
              prop="dtime"
              label="报修时间"
          >
          </el-table-column>
          <el-table-column
              label="操作"
              width="100">
            <template slot-scope="scope">
              <el-button type="text" size="small" @click="showEdit(scope.row)">编辑</el-button>
              <el-button type="text" size="small" @click="del(scope.row.id)">删除</el-button>
            </template>
<el-form :model="form" :rules="rules" ref="form" label-width="100px" style="width: 600px">
        <el-form-item label="订单号：" prop="str">
          <el-input v-model="form.str"></el-input>
        </el-form-item>
        <el-form-item label="住户姓名：" prop="num">
           <el-input v-model="form.num"></el-input>
      </el-form-item>

        <el-form-item label="联系电话：" prop="price">
           <el-input v-model="form.price"></el-input>
        </el-form-item>

         <el-form-item label="报修描述：" prop="pic">
          <el-input v-model="form.pic"></el-input>
        </el-form-item>

        <el-form-item label="报修日期：" prop="dtime">
          <el-date-picker
              v-model="form.dtime"
              type="datetime"
              placeholder="选择日期时间"
              value-format="yyyy-MM-dd HH:mm:ss"
          >
          </el-date-picker>
        </el-form-item>
      </el-form> 
      <span slot="footer" class="dialog-footer">
          <el-button @click="dialogClose">取 消</el-button>
          <el-button type="primary" @click="submitForm('form')">确 定</el-button>
        </span>
    </el-dialog>
repair() {
      if(this.aTable.pic == '' || this.aTable.pic == null) {
        return
      }
      this.$http.post('/a/repair',this.aTable).then(res=>{
        if(res.data.code === 200) {
          this.$message.success('报修成功')
          this.dialogFormVisible = false
        }
       })
    },
    startRepair(oid){
      this.aTable.str = oid
      this.dialogFormVisible = true
    },
