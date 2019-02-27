<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="归属宠物id" prop="petId">
      <el-input v-model="dataForm.petId" placeholder="归属宠物id"></el-input>
    </el-form-item>
    <el-form-item label="视频名称" prop="name">
      <el-input v-model="dataForm.name" placeholder="视频名称"></el-input>
    </el-form-item>
    <el-form-item label="视频分类，字典码" prop="type">
      <el-input v-model="dataForm.type" placeholder="视频分类，字典码"></el-input>
    </el-form-item>
    <el-form-item label="文件名称" prop="fileName">
      <el-input v-model="dataForm.fileName" placeholder="文件名称"></el-input>
    </el-form-item>
    <el-form-item label="文件地址" prop="fileUrl">
      <el-input v-model="dataForm.fileUrl" placeholder="文件地址"></el-input>
    </el-form-item>
    <el-form-item label="文件类型" prop="fileType">
      <el-input v-model="dataForm.fileType" placeholder="文件类型"></el-input>
    </el-form-item>
    <el-form-item label="文件大小" prop="fileSize">
      <el-input v-model="dataForm.fileSize" placeholder="文件大小"></el-input>
    </el-form-item>
    <el-form-item label="文件介绍" prop="remark">
      <el-input v-model="dataForm.remark" placeholder="文件介绍"></el-input>
    </el-form-item>
    <el-form-item label="视频截图" prop="filePic">
      <el-input v-model="dataForm.filePic" placeholder="视频截图"></el-input>
    </el-form-item>
    <el-form-item label="创建日期" prop="createTime">
      <el-input v-model="dataForm.createTime" placeholder="创建日期"></el-input>
    </el-form-item>
    <el-form-item label="视频时长" prop="videoLength">
      <el-input v-model="dataForm.videoLength" placeholder="视频时长"></el-input>
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
          petId: '',
          name: '',
          type: '',
          fileName: '',
          fileUrl: '',
          fileType: '',
          fileSize: '',
          remark: '',
          filePic: '',
          createTime: '',
          videoLength: ''
        },
        dataRule: {
          petId: [
            { required: true, message: '归属宠物id不能为空', trigger: 'blur' }
          ],
          name: [
            { required: true, message: '视频名称不能为空', trigger: 'blur' }
          ],
          type: [
            { required: true, message: '视频分类，字典码不能为空', trigger: 'blur' }
          ],
          fileName: [
            { required: true, message: '文件名称不能为空', trigger: 'blur' }
          ],
          fileUrl: [
            { required: true, message: '文件地址不能为空', trigger: 'blur' }
          ],
          fileType: [
            { required: true, message: '文件类型不能为空', trigger: 'blur' }
          ],
          fileSize: [
            { required: true, message: '文件大小不能为空', trigger: 'blur' }
          ],
          remark: [
            { required: true, message: '文件介绍不能为空', trigger: 'blur' }
          ],
          filePic: [
            { required: true, message: '视频截图不能为空', trigger: 'blur' }
          ],
          createTime: [
            { required: true, message: '创建日期不能为空', trigger: 'blur' }
          ],
          videoLength: [
            { required: true, message: '视频时长不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/manage/videoinfo/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.petId = data.videoinfo.petId
                this.dataForm.name = data.videoinfo.name
                this.dataForm.type = data.videoinfo.type
                this.dataForm.fileName = data.videoinfo.fileName
                this.dataForm.fileUrl = data.videoinfo.fileUrl
                this.dataForm.fileType = data.videoinfo.fileType
                this.dataForm.fileSize = data.videoinfo.fileSize
                this.dataForm.remark = data.videoinfo.remark
                this.dataForm.filePic = data.videoinfo.filePic
                this.dataForm.createTime = data.videoinfo.createTime
                this.dataForm.videoLength = data.videoinfo.videoLength
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
              url: this.$http.adornUrl(`/manage/videoinfo/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'petId': this.dataForm.petId,
                'name': this.dataForm.name,
                'type': this.dataForm.type,
                'fileName': this.dataForm.fileName,
                'fileUrl': this.dataForm.fileUrl,
                'fileType': this.dataForm.fileType,
                'fileSize': this.dataForm.fileSize,
                'remark': this.dataForm.remark,
                'filePic': this.dataForm.filePic,
                'createTime': this.dataForm.createTime,
                'videoLength': this.dataForm.videoLength
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
