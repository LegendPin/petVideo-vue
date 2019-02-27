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
                        :show-file-list="true"
                        :on-success="handleHeadPicSuccess"
                        accept=".jpg,.png,.jpeg">
                        <img v-if="dataForm.headPic" src="dataForm.headPic" class="avatar">
                        <i v-else class="el-icon-plus avatar-uploader-icon"></i>
                    </el-upload>
                </el-col>
            </el-row>
            <el-form-item label="视频介绍：" prop="remark" style="margin-top: 25px">
                <el-input type="textarea" :row="4"  class="vp-textarea"></el-input>
            </el-form-item>
        </el-form>
        <img v-if="1" src="/petfile/favicon.ico" class="avatar">
        <el-button @click="getStatic(`/petfile/favicon.ico`)"></el-button>
        <!--<el-card class="box-card" style="width: 50%;left: 100px;position: relative">-->
            <!--<el-upload-->
                <!--ref="upload"-->
                <!--:show-file-list="true"-->
                <!--:auto-upload="true"-->
                <!--accept=".mp4,.avi,.rmvb,.rm,.mpeg,.3gp,.wmv,.flv">-->
                <!--<el-button slot="trigger" size="small" type="primary">选取文件</el-button>-->
                <!--<div slot="tip" class="el-upload__tip">仅支持.mp4,.avi,.rmvb,.rm,.mpeg,.3gp,.wmv,.flv格式</div>-->
            <!--</el-upload>-->
        <!--</el-card>-->
    </div>
</template>

<script>
    import Vue from 'vue';
    export default {
        name: "video-public",
        data(){
            return {
                tabList:[],          //视频分类列表
                dataForm:{
                    name: '',        //视频标题
                    type: '',        //视频分类
                    filename: '',    //文件名称
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
            /********************文件操作相关函数**************************/
            //动态返回文件上传地址
            uploadHeadPic(){
                return this.$http.adornUrl(`/manage/videoinfo/uploadHead`)+"?token="+Vue.cookie.get('token');
            },
            handleHeadPicSuccess(data){
                if (data && data.code === 0) {
                    this.dataForm.headPic = data.fileUrl;

                    console.log(this.dataForm.headPic)
                    this.$message({
                        message: '头像上传成功',
                        type: 'success',
                    })
                } else {
                    this.$message.error(data.msg)
                }
            },

            uploadVideo(){
                return this.$http.adornUrl(`/manage/videoinfo/uploadVideo`)+"?token="+Vue.cookie.get('token');
            },
            //获取文件
            getStatic(url){
                window.location.href=this.$http.adornUrl(url);
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
