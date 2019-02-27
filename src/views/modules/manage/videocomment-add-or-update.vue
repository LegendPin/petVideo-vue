<template>
  <el-dialog
    :title="!dataForm.id ? '新增' : '修改'"
    :close-on-click-modal="false"
    :visible.sync="visible">
    <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="80px">
    <el-form-item label="归属视频id" prop="videoId">
      <el-input v-model="dataForm.videoId" placeholder="归属视频id"></el-input>
    </el-form-item>
    <el-form-item label="评论用户id" prop="commentUser">
      <el-input v-model="dataForm.commentUser" placeholder="评论用户id"></el-input>
    </el-form-item>
    <el-form-item label="评论用户姓名" prop="commentName">
      <el-input v-model="dataForm.commentName" placeholder="评论用户姓名"></el-input>
    </el-form-item>
    <el-form-item label="评论人头像" prop="commentPic">
      <el-input v-model="dataForm.commentPic" placeholder="评论人头像"></el-input>
    </el-form-item>
    <el-form-item label="评论内容" prop="content">
      <el-input v-model="dataForm.content" placeholder="评论内容"></el-input>
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
          videoId: '',
          commentUser: '',
          commentName: '',
          commentPic: '',
          content: '',
          createTime: ''
        },
        dataRule: {
          videoId: [
            { required: true, message: '归属视频id不能为空', trigger: 'blur' }
          ],
          commentUser: [
            { required: true, message: '评论用户id不能为空', trigger: 'blur' }
          ],
          commentName: [
            { required: true, message: '评论用户姓名不能为空', trigger: 'blur' }
          ],
          commentPic: [
            { required: true, message: '评论人头像不能为空', trigger: 'blur' }
          ],
          content: [
            { required: true, message: '评论内容不能为空', trigger: 'blur' }
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
              url: this.$http.adornUrl(`/manage/videocomment/info/${this.dataForm.id}`),
              method: 'get',
              params: this.$http.adornParams()
            }).then(({data}) => {
              if (data && data.code === 0) {
                this.dataForm.videoId = data.videocomment.videoId
                this.dataForm.commentUser = data.videocomment.commentUser
                this.dataForm.commentName = data.videocomment.commentName
                this.dataForm.commentPic = data.videocomment.commentPic
                this.dataForm.content = data.videocomment.content
                this.dataForm.createTime = data.videocomment.createTime
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
              url: this.$http.adornUrl(`/manage/videocomment/${!this.dataForm.id ? 'save' : 'update'}`),
              method: 'post',
              data: this.$http.adornData({
                'id': this.dataForm.id || undefined,
                'videoId': this.dataForm.videoId,
                'commentUser': this.dataForm.commentUser,
                'commentName': this.dataForm.commentName,
                'commentPic': this.dataForm.commentPic,
                'content': this.dataForm.content,
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
