<template>
    <div class="vp-main">
        <el-form :model="dataForm" :rules="dataRule" ref="dataForm" @keyup.enter.native="dataFormSubmit()" label-width="100px">
            <el-row>
                <el-col :span="12">
                    <el-form-item label="标题：" prop="name" style="margin-top: 50px">
                        <el-input style="width: 100%" v-model="dataForm.name"></el-input>
                    </el-form-item>
                    <el-form-item label="类型：" prop="type" style="margin-top: 50px">
                        <el-select
                            clearable
                            size="medium"
                            style="width: 100%"
                            v-model="dataForm.type">
                            <el-option
                                v-for="item in tabList"
                                :key="item.dictCode"
                                :label="item.dictName"
                                :value="item.dictCode">
                            </el-option>
                        </el-select>
                    </el-form-item>
                </el-col>
                <el-col :span="5" :offset="5">
                    <el-upload
                        class="head-pic-upload"
                        :action="uploadHeadPic()"
                        :show-file-list="false"
                        :on-success="handleHeadPicSuccess"
                        accept=".jpg,.png,.jpeg,.bmp,.gif">
                        <img v-if="imageUrl" :src="imageUrl" class="avatar">
                        <i v-else class="el-icon-plus avatar-uploader-icon"></i>
                    </el-upload>
                </el-col>
            </el-row>
            <el-form-item label="视频介绍：" prop="remark" style="margin-top: 25px">
                <el-input type="textarea" :row="4"  class="vp-textarea" v-model="dataForm.remark"></el-input>
            </el-form-item>
        </el-form>
        <el-card class="box-card" style="width: 50%;left: 100px;position: relative">
            <el-upload
                ref="upload-video"
                :action="uploadVideo()"
                :show-file-list="true"
                :auto-upload="true"
                :on-success="handleVideoSuccess"
                :limit="1"
                :on-exceed="handleVideoOver"
                accept=".mp4,.avi,.rmvb,.rm,.mpeg,.3gp,.wmv,.flv">
                <el-button slot="trigger" size="small" type="primary">选取文件</el-button>
                <div slot="tip" class="el-upload__tip">仅支持.mp4,.avi,.rmvb,.rm,.mpeg,.3gp,.wmv,.flv格式</div>
            </el-upload>
        </el-card>
        <el-button type="primary" size="large" @click="publicVideo()" class="vp-btn-sumbmit">发布视频</el-button>
    </div>
</template>

<script>
    import Vue from 'vue';
    export default {
        name: "video-public",
        data(){
            return {
                tabList:[],          //视频分类列表
                imageUrl:null,
                dataForm:{
                    name: '',        //视频标题
                    type: '',        //视频分类
                    fileName: '',    //文件名称
                    fileUrl: '',     //文件地址
                    filePic: '',     //视频截图路径
                    remark: ''       //视频详细描述
                },
                dataRule: {
                    name: [
                        { required: true, message: '视频标题不能为空', trigger: 'blur' }
                    ],
                    type: [
                        { required: true, message: '视频分类不能为空', trigger: 'blur' }
                    ],
                    remark: [
                        { required: true, message: '视频详细描述不能为空', trigger: 'blur' }
                    ]
                }
            }
        },
        activated(){
            this.getDictListByParent();
        },
        methods: {
            //获取视频分类列表
            getDictListByParent(){
                this.$http({
                    url: this.$http.adornUrl('/manage/dict/getDictListByParent'),
                    method: 'get',
                    params: this.$http.adornParams({
                        code: 101
                    })
                }).then(({data}) => {
                    if (data && data.code === 0) {
                        this.tabList = data.list;
                    } else {
                        this.$message.error(data.msg)
                    }
                })
            },
            //视频提交按钮点击回调
            publicVideo(){
                //判断参数有效性
                this.$refs['dataForm'].validate((valid) => {
                    if (valid){
                        if (this.dataForm.fileName === '' || this.dataForm.fileUrl === '' ||
                            this.dataForm.fileName == null || this.dataForm.fileUrl == null ||
                            this.dataForm.filePic == null || this.dataForm.filePic === ''){
                            console.log("name: " + this.dataForm.fileName)
                            console.log("url:" + this.dataForm.fileUrl)
                            console.log("pic:" + this.dataForm.filePic)
                            this.$message.warning("文件和头像不能为空")
                            return;
                        }
                        this.$http({
                            url: this.$http.adornUrl('/manage/videoinfo/save'),
                            method: 'post',
                            data: this.$http.adornData({
                                name: this.dataForm.name,
                                type: this.dataForm.type,
                                fileName: this.dataForm.fileName,
                                fileUrl: this.dataForm.fileUrl,
                                remark: this.dataForm.remark,
                                filePic: this.dataForm.filePic
                            })
                        }).then(({data}) => {
                            if (data && data.code === 0) {
                                this.$message.success("发布视频成功");
                                this.$router.push({ name: 'home' }); //跳转回首页
                            } else {
                                this.$message.error(data.msg)
                            }
                        })
                    }

                })
            },
            /********************文件操作相关函数**************************/
            //动态返回文件上传地址
            uploadHeadPic(){
                return this.$http.adornUrl(`/manage/videoinfo/uploadHead`)+"?token="+Vue.cookie.get('token');
            },
            //视频截图上传成功回调
            handleHeadPicSuccess(data, file){
                if (data && data.code === 0) {
                    this.imageUrl = URL.createObjectURL(file.raw);
                    this.dataForm.filePic = data.fileUrl;
                    console.log(this.dataForm.filePic)
                    this.$message({
                        message: '头像上传成功',
                        type: 'success'
                    })
                } else {
                    this.$message.error(data.msg)
                }
            },
            //构建视频文件请求路径
            uploadVideo(){
                return this.$http.adornUrl(`/manage/videoinfo/uploadVideo`)+"?token="+Vue.cookie.get('token');
            },
            //视频文件上传成功回调
            handleVideoSuccess(data){
                if (data && data.code === 0) {
                    this.dataForm.fileName = data.filename;
                    this.dataForm.fileUrl = data.fileUrl;
                    console.log(this.dataForm.fileName);
                    console.log(this.dataForm.fileUrl);
                    this.$message({
                        message: '视频上传成功',
                        type: 'success'
                    })
                } else {
                    this.$message.error(data.msg)
                }
            },
            //文件数量超出回调
            handleVideoOver(){
                this.$message.warning('只能上传一个视频文件')
            }
        }
    }
</script>

<style scoped>
    .vp-main{
        position: relative;
        width: 80%;
        left: 10%;
        top: 50px;
    }
    .vp-textarea >>> .el-textarea__inner{
        height: 150px;
        width: 84%;
    }
    .vp-btn-sumbmit{
        position: relative;
        left: 20%;
        margin-top: 50px;
    }
    .avatar-uploader .el-upload {
        border: 1px dashed #d9d9d9;
        border-radius: 6px;
        cursor: pointer;
        position: relative;
        overflow: hidden;
    }
    .avatar-uploader .el-upload:hover {
        border-color: #409EFF;
    }
    .avatar-uploader-icon {
        font-size: 28px;
        color: #8c939d;
        width: 178px;
        height: 178px;
        line-height: 178px;
        text-align: center;
        border: 1px solid #d9d9d9;
    }
    .avatar {
        width: 178px;
        height: 178px;
        display: block;
    }
</style>
