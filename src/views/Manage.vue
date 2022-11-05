<template>
    <el-container style="height: 100%">
      <el-aside :width="sidewidth+'px'" style="background-color: rgb(238, 241, 246);height: 100%;box-shadow: 2px 0 6px rgb(0 21 41/35%)">
        <el-menu :default-openeds="['1', '3']" style="min-height: 100%;overflow-x: hidden"
                  background-color="#66ccff"
                  text-color="#fff"
                  active-text-color="#ee0000"
                  :collapse-transition="false"
                  :collapse="isCollapse">
          <el-menu-item>
            <i class="el-icon-message"></i> <span slot="title">主页</span>
          </el-menu-item>
          <el-menu-item>
            <i class="el-icon-message"></i><span slot="title">员工信息</span>
          </el-menu-item>
          <el-menu-item>
            <i class="el-icon-message"></i><span slot="title">设备管理</span>
          </el-menu-item>
          <el-menu-item>
            <i class="el-icon-message"></i><span slot="title">维修管理</span>
          </el-menu-item>
          <el-menu-item>
            <i class="el-icon-message"></i><span slot="title">部门管理</span>
          </el-menu-item>
          <el-menu-item>
            <i class="el-icon-message"></i><span slot="title">租借信息</span>
          </el-menu-item>
          <el-menu-item>
            <i class="el-icon-message"></i><span slot="title">采购管理</span>
          </el-menu-item>
        </el-menu>
      </el-aside>
      <el-container>
        <el-header style="font-size: 12px; border-bottom: 1px solid #66ccff;line-height: 60px;display: flex">
          <div style="flex: 1;font-size: 18px">
            <span :class="collapseBtnClass" style="cursor: pointer" @click="collapse"></span>
          </div>
          <div style="margin-right:24%;flex: 2;height: 60px;line-height: 60px;text-align: center">
            <img src="../assets/icon.jpeg" style="width: 30px;position: relative;top: 4.5px"/>
            <b style="color: black;font-size: 30px;cursor: pointer">企业设备管理系统</b>
          </div>
          <el-dropdown style="width: 80px;cursor: pointer">
            <span>王小虎</span> <i class="el-icon-arrow-down" style="margin-left: 5px" ></i>
            <el-dropdown-menu slot="dropdown">
              <el-dropdown-item>个人信息</el-dropdown-item>
              <el-dropdown-item>退出</el-dropdown-item>
            </el-dropdown-menu>
          </el-dropdown>

        </el-header>

        <el-main>
          <div style="padding: 10px 0">
            <el-input placeholder="请输入设备名称" style="width: 200px" suffix-icon="el-icon-search" type="text" v-model="equipmentName"></el-input>
            <el-input placeholder="请输入设备编号" class="ml-5" style="width: 200px" suffix-icon="el-icon-price-tag" type="text" v-model="equipmentNo"></el-input>
            <el-input placeholder="请输入设备部门" class="ml-5" style="width: 200px" suffix-icon="el-icon-house" type="text" v-model="department">
            </el-input><el-button class="ml-5" type="primary" @click="load">搜索</el-button>
            <el-button class="ml-5" type="primary" @click="reset">重置</el-button>
          </div>
          <div style="padding: 10px 0">
            <el-button type="primary" @click="handleAdd">新增<i class="el-icon-circle-plus-outline ml-5"></i></el-button>
            <el-popconfirm
                class="ml-5"
                confirm-button-text='确定'
                cancel-button-text='我再想想'
                icon="el-icon-info"
                icon-color="red"
                title="您确定要批量删除这些数据吗？"
                @confirm="delbatch">
            <el-button type="primary" slot="reference">批量删除<i class="el-icon-remove-outline ml-5"></i></el-button>
            </el-popconfirm>
            <el-button type="primary" class="ml-5">导入<i class="el-icon-download  ml-5"></i></el-button>
            <el-button type="primary">导出<i class="el-icon-upload2 ml-5"></i></el-button>
          </div>
          <el-table :data="tableData" border stripe :header-cell-class-name="headerBg"  @selection-change="handleSelectionChange">
            <el-table-column
                type="selection"
                width="55">
            </el-table-column>
            <el-table-column prop="id" label="ID" width="120">
            </el-table-column>
            <el-table-column prop="equipmentNo" label="设备编号" width="120">
            </el-table-column>
            <el-table-column prop="equipmentName" label="设备名称" width="140">
            </el-table-column>
            <el-table-column prop="equipmentRent" label="租借状态" width="140">
            </el-table-column>
            <el-table-column prop="equipmentStatus"  width="140" label="设备状态">
            </el-table-column>
            <el-table-column prop="department"  width="140" label="所属部门">
            </el-table-column>
            <el-table-column label="操作">
              <template slot-scope="scope">
                <el-button @click="handleEdit(scope.row)">编辑<i class="el-icon-edit ml-5"></i></el-button>
                <template>
                  <el-popconfirm
                      class="ml-5"
                      confirm-button-text='确定'
                      cancel-button-text='我再想想'
                      icon="el-icon-info"
                      icon-color="red"
                      title="您确定要删除吗？"
                      @confirm="del(scope.row.id)">
                    <el-button slot="reference">删除<i class="el-icon-remove-outline"></i></el-button>
                  </el-popconfirm>
                </template>
                <el-button class="ml-5">维修<i class="el-icon-set-up ml-5"></i></el-button>
              </template>
            </el-table-column>
          </el-table>
          <div style="padding: 10px 0">
            <el-pagination
                @size-change="handleSizeChange"
                @current-change="handleCurrentChange"
                :current-page="pageNum"
                :page-sizes="[2, 5, 10, 20]"
                :page-size="pageSize"
                layout="total, sizes, prev, pager, next, jumper"
                :total="total">
            </el-pagination>
          </div>

          <el-dialog title="设备信息" :visible.sync="dialogFormVisible" width="35%">
            <el-form :model="form" label-width="100px">
              <el-form-item label="ID">
                <el-input v-model="form.id" disabled autocomplete="off"></el-input>
              </el-form-item>
              <el-form-item label="设备编号">
                <el-input v-model="form.equipmentNo" autocomplete="off"></el-input>
              </el-form-item>
              <el-form-item label="设备名称">
                <el-input v-model="form.equipmentName" autocomplete="off"></el-input>
              </el-form-item>
              <el-form-item label="设备状态">
                <el-select v-model="form.equipmentStatus" placeholder="请选择活动区域">
                  <el-option label="优秀" value="优秀"></el-option>
                  <el-option label="良好" value="良好"></el-option>
                  <el-option label="损坏" value="损坏"></el-option>
                </el-select>
              </el-form-item>
              <el-form-item label="设备所属部门">
                <el-select v-model="form.department" placeholder="请选择活动区域">
                  <el-option label="人事部" value="1"></el-option>
                  <el-option label="研发部" value="2"></el-option>
                  <el-option label="财务部" value="3"></el-option>
                  <el-option label="运营部" value="4"></el-option>
                  <el-option label="客服部" value="5"></el-option>
                  <el-option label="审核部" value="6"></el-option>
                  <el-option label="管理部" value="7"></el-option>
                </el-select>
              </el-form-item>
              <el-form-item label="租借状态">
                <el-select v-model="form.equipmentRent" placeholder="请选择活动区域">
                  <el-option label="未租借" value="未租借"></el-option>
                  <el-option label="已租借" value="已租借"></el-option>
                </el-select>
              </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
              <el-button @click="dialogFormVisible = false">取 消</el-button>
              <el-button type="primary" @click="saveOrUpdate">确 定</el-button>
            </div>
          </el-dialog>
        </el-main>
      </el-container>
    </el-container>
</template>

<script>
// @ is an alias to /src
import HelloWorld from '@/components/HelloWorld.vue'

export default {
  name: 'HomeView',
  components: {
    HelloWorld
  },
  data(){
    return {
      tableData: [],
      total: 0,
      pageNum: 1,
      pageSize: 2,
      equipmentName: "",
      equipmentNo: "",
      department: "",
      dialogFormVisible: false,
      form: {},
      multipleSelection: [],
      collapseBtnClass: "el-icon-s-fold",
      isCollapse: false,
      sidewidth:200,
      headerBg:"headerBg"
    }
  },
  created() {
   this.load()
  },
  methods: {
    collapse(){
      this.isCollapse=!this.isCollapse
      if(this.isCollapse){
        this.sidewidth=64
        this.collapseBtnClass="el-icon-s-fold"
      }else{
        this.sidewidth=200
      }
    },
    load(){
      this.request.get("/equipment/page",{
        params: {
          pageNum: this.pageNum,
          pageSize: this.pageSize,
          equipmentName: this.equipmentName,
          equipmentNo: this.equipmentNo,
          department: this.department
        }
      }).then(res=>{
        this.tableData=res.data
        this.total=res.total
      })
    },
    reset(){
      this.equipmentName=""
      this.equipmentNo=""
      this.department=""
      this.load()
    },
    saveOrUpdate(){
      this.request.post("/equipment/saveOrUpdate",this.form).then(res=>{
        if(res){
          if(this.form.id==null) {
            this.$message.success("新增成功")
            this.dialogFormVisible = false
            this.load()
          }
          if(this.form.id!=null){
            this.$message.success("修改成功")
            this.dialogFormVisible = false
            this.load()
          }
        }else{
          if(this.form.id==null) {
            this.$message.error("新增失败")
          }
          if(this.form.id!=null) {
            this.$message.error("修改失败")
          }
        }
      })
    },
    del(id){
      this.request.delete("/equipment/delete/"+id).then(res=>{
        if(res){
          this.$message.success("删除成功")
          this.load()
        }else{
          this.$message.error("删除失败")
          this.load()
        }
      })
    },
    handleSelectionChange(val){
      this.multipleSelection=val
      this.request.delete("/equipment/batch")
    },
    handleAdd(){
      this.dialogFormVisible=true
      this.form={}
    },
    delbatch(){
      let ids=this.multipleSelection.map(v=>v.id)
      this.request.post("/equipment/batch",ids).then(res=>{
        if(res){
          this.$message.success("批量删除成功")
          this.load()
        }else{
          this.$message.error("批量删除失败")
          this.load()
        }
      })
    },
    handleEdit(row){
      this.dialogFormVisible=true;
      this.form=Object.assign({},row)
    },
    handleSizeChange(pageSize){
      this.pageSize=pageSize
      this.load()
    },
    handleCurrentChange(pageNum){
      this.pageNum=pageNum
      this.load()
    },
  }
}
</script>

<style>
.headerBg{
  background: #eee!important;
}
</style>
