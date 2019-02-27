<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="关联账号id" prop="userId">
      <el-input v-model="dataForm.userId" placeholder="关联账号id"></el-input>
    </el-form-item>
    <el-form-item label="宠物名称" prop="name">
      <el-input v-model="dataForm.name" placeholder="宠物名称"></el-input>
    </el-form-item>
    <el-form-item label="头像文件路径" prop="headPic">
      <el-input v-model="dataForm.headPic" placeholder="头像文件路径"></el-input>
    </el-form-item>
    <el-form-item label="性别(0:男 1：女 2：不限)" prop="sex">
      <el-input v-model="dataForm.sex" placeholder="性别(0:男 1：女 2：不限)"></el-input>
    </el-form-item>
    <el-form-item label="种类，字典码" prop="type">
      <el-input v-model="dataForm.type" placeholder="种类，字典码"></el-input>
    </el-form-item>
    <el-form-item label="出生日期" prop="birthday">
      <el-input v-model="dataForm.birthday" placeholder="出生日期"></el-input>
    </el-form-item>
    <el-form-item label="电话号码" prop="phone">
      <el-input v-model="dataForm.phone" placeholder="电话号码"></el-input>
    </el-form-item>
    <el-form-item label="地址" prop="address">
      <el-input v-model="dataForm.address" placeholder="地址"></el-input>
    </el-form-item>
    <el-form-item label="个人介绍" prop="remark">
      <el-input v-model="dataForm.remark" placeholder="个人介绍"></el-input>
    </el-form-item>
    <el-form-item label="铲屎官姓名" prop="owner">
      <el-input v-model="dataForm.owner" placeholder="铲屎官姓名"></el-input>
    </el-form-item>
    <el-form-item label="创建时间" prop="createTime">
      <el-input v-model="dataForm.createTime" placeholder="创建时间"></el-input>
    </el-form-item>
    </el-form>
    <span slot="footer" class="dialog-footer">
      <el-button @click="visible = false">取消</el-button>
      <el-button type="primary" @click="dataFormSubmit()">确定</el-button>
    </span>
  </el-dialog>
</template>

<script>
  export default {
    data () {
      return {
        visible: false,
        dataForm: {
          id: 0,
          userId: '',
          name: '',
          headPic: '',
          sex: '',
          type: '',
          birthday: '',
          phone: '',
          address: '',
          remark: '',
          owner: '',
          createTime: ''
        },
        dataRule: {
          userId: [
            { required: true, message: '关联账号id不能为空', trigger: 'blur' }
          ],
          name: [
            { required: true, message: '宠物名称不能为空', trigger: 'blur' }
          ],
          headPic: [
            { required: true, message: '头像文件路径不能为空', trigger: 'blur' }
          ],
          sex: [
            { required: true, message: '性别(0:男 1：女 2：不限)不能为空', trigger: 'blur' }
          ],
          type: [
            { required: true, message: '种类，字典码不能为空', trigger: 'blur' }
          ],
          birthday: [
            { required: true, message: '出生日期不能为空', trigger: 'blur' }
          ],
          phone: [
            { required: true, message: '电话号码不能为空', trigger: 'blur' }
          ],
          address: [
            { required: true, message: '地址不能为空', trigger: 'blur' }
          ],
          remark: [
            { required: true, message: '个人介绍不能为空', trigger: 'blur' }
          ],
          owner: [
            { required: true, message: '铲屎官姓名不能为空', trigger: 'blur' }
          ],
          createTime: [
            { required: true, message: '创建时间不能为空', trigger: 'blur' }
          ]
        }
      }
    },
    methods: {
      init (id) {
        this.dataForm.id = id || 0
        this.visible = true
        this.$nextTick(() => {
          this.$refs['dataForm'].resetFields()
          if (this.dataForm.id) {
            this.$http({
              url: this.$http.adornUrl(`/manage/peinfo/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.userId = data.peinfo.userId
                this.dataForm.name = data.peinfo.name
                this.dataForm.headPic = data.peinfo.headPic
                this.dataForm.sex = data.peinfo.sex
                this.dataForm.type = data.peinfo.type
                this.dataForm.birthday = data.peinfo.birthday
                this.dataForm.phone = data.peinfo.phone
                this.dataForm.address = data.peinfo.address
                this.dataForm.remark = data.peinfo.remark
                this.dataForm.owner = data.peinfo.owner
                this.dataForm.createTime = data.peinfo.createTime
              }
            })
          }
        })
      },
      // 表单提交
      dataFormSubmit () {
        this.$refs['dataForm'].validate((valid) => {
          if (valid) {
            this.$http({
              url: this.$http.adornUrl(`/manage/peinfo/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'userId': this.dataForm.userId,
                'name': this.dataForm.name,
                'headPic': this.dataForm.headPic,
                'sex': this.dataForm.sex,
                'type': this.dataForm.type,
                'birthday': this.dataForm.birthday,
                'phone': this.dataForm.phone,
                'address': this.dataForm.address,
                'remark': this.dataForm.remark,
                'owner': this.dataForm.owner,
                'createTime': this.dataForm.createTime
              })
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.$message({
                  message: '操作成功',
                  type: 'success',
                  duration: 1500,
                  onClose: () => {
                    this.visible = false
                    this.$emit('refreshDataList')
                  }
                })
              } else {
                this.$message.error(data.msg)
              }
            })
          }
        })
      }
    }
  }
</script>
