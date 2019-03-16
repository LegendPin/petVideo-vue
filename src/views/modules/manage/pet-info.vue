<template>
    <div>
        <el-card class="box-card vp-main" >
            <div slot="header" class="clearfix">
                <el-row>
                    <el-col :span="4" :offset="10">
                    <h2 style="width: 200px;">个人简历</h2>
                    </el-col>
                    <el-col :span="2" :offset="8">
                    <el-button type="primary" size="small" style="margin-top: 16px" @click="submitPetInfo">保存修改</el-button>
                    </el-col>
                </el-row>
            </div>
            <el-form :model="dataForm" ref="dataForm" label-width="100px" class="vp-form">
                <el-row>
                    <el-col :span="11">
                        <el-form-item label="宠物名称：" prop="name" style="margin-top: 25px">
                            <el-input style="width: 100%" v-model="dataForm.name"></el-input>
                        </el-form-item>
                        <el-form-item label="出生日期：" prop="birthday"  style="margin-top: 50px">
                            <el-date-picker
                                v-model="dataForm.birthday"
                                style="width: 100%"
                                type="date"
                                value-format="yyyy-MM-dd"
                                format="yyyy-MM-dd">
                            </el-date-picker>
                        </el-form-item>
                    </el-col>
                    <el-col :span="5" :offset="7">
                        <el-upload
                            class="head-pic-upload"
                            :action="uploadHeadPic()"
                            :show-file-list="false"
                            :on-success="handleHeadPicSuccess"
                            accept=".jpg,.png,.jpeg,.bmp">
                            <img v-if="imageUrl" :src="imageUrl" class="avatar">
                            <img v-else-if="dataForm.headPic" :src="$http.adornUrl(dataForm.headPic)" class="avatar">
                            <i v-else class="el-icon-plus avatar-uploader-icon"></i>
                        </el-upload>
                    </el-col>
                </el-row>
                <el-row style="margin-top: 25px">
                    <el-col :span="11">
                        <el-form-item label="种族：" prop="type">
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
                    <el-col :span="11" :offset="1">
                        <el-form-item label="性别：" prop="sex">
                            <el-select clearable size="medium" style="width: 100%" v-model="dataForm.sex">
                                <el-option label="男" value="0"></el-option>
                                <el-option label="女" value="1"></el-option>
                                <el-option label="不详" value="2"></el-option>
                            </el-select>
                        </el-form-item>
                    </el-col>
                </el-row>
                <el-row style="margin-top: 25px">
                    <el-col :span="11">
                        <el-form-item label="铲屎官姓名：" prop="owner">
                            <el-input style="width: 100%" v-model="dataForm.owner"></el-input>
                        </el-form-item>
                    </el-col>
                    <el-col :span="11" :offset="1">
                        <el-form-item label="联系方式：" prop="phone">
                            <el-input style="width: 100%" v-model="dataForm.phone"></el-input>
                        </el-form-item>
                    </el-col>
                </el-row>
                <el-row style="margin-top: 25px">
                    <el-form-item label="家庭住址：" prop="address">
                        <el-col :span="23">
                            <el-input style="width: 100%" v-model="dataForm.address"></el-input>
                        </el-col>
                    </el-form-item>
                </el-row>
                <el-row style="margin-top: 25px">
                    <el-form-item label="个人介绍：" prop="remark">
                        <el-col :span="23">
                            <el-input type="textarea" :row="3"  class="vp-textarea" v-model="dataForm.remark"></el-input>
                        </el-col>
                    </el-form-item>
                </el-row>
            </el-form>
        </el-card>
    </div>
</template>

<script>
    import Vue from 'vue';
    export default {
        name: "pet-info",
        data(){
            return {
                tabList:[],          //视频分类列表
                imageUrl:null,
                dataForm:{
                    id:'',           //信息编号
                    name: '',        //宠物名称
                    sex:'',          //性别
                    type: '',        //种类
                    birthday:'',     //生日
                    phone: '',       //电话
                    address: '',     //地址
                    headPic: '',     //头像路径
                    remark: ''       //个人简介
                }
            }
        },
        activated(){
            this.getDictListByParent();
            this.getPetInfo();
        },
        methods: {
            //获取视频分类列表
            getDictListByParent() {
                this.$http({
                    url: this.$http.adornUrl('/manage/dict/getDictListByParent'),
                    method: 'get',
                    params: this.$http.adornParams({
                        code: 100
                    })
                }).then(({data}) => {
                    if (data && data.code === 0) {
                        this.tabList = data.list;
                    } else {
                        this.$message.error(data.msg)
                    }
                })
            },
            //获取当前用户的宠物信息
            getPetInfo(){
                this.$http({
                    url: this.$http.adornUrl('/manage/petInfo/info'),
                    method: 'get',
                    params: this.$http.adornParams({})
                }).then(({data}) => {
                    if (data && data.code === 0) {
                        let info = data.info;
                        this.dataForm.id = info.id;
                        this.dataForm.name = info.name;
                        this.dataForm.headPic = info.headPic;
                        this.dataForm.sex = info.sex;
                        this.dataForm.type = info.type;
                        this.dataForm.birthday = info.birthday;
                        this.dataForm.phone = info.phone;
                        this.dataForm.address = info.address;
                        this.dataForm.remark = info.remark;
                        this.dataForm.owner = info.owner;
                    } else {
                        this.$message.error(data.msg)
                    }
                })
            },
            //个人信息修改点击回调
            submitPetInfo(){
                this.$http({
                    url: this.$http.adornUrl('/manage/petInfo/update'),
                    method: 'post',
                    data: this.$http.adornData({
                        id: this.dataForm.id,
                        name: this.dataForm.name,
                        type: this.dataForm.type,
                        sex: this.dataForm.sex,
                        birthday: this.dataForm.birthday,
                        phone: this.dataForm.phone,
                        address: this.dataForm.address,
                        owner: this.dataForm.owner,
                        remark: this.dataForm.remark,
                        headPic: this.dataForm.headPic
                    })
                }).then(({data}) => {
                    if (data && data.code === 0) {
                        this.$message.success("修改个人信息成功");
                        this.$router.push({ name: 'home' }); //跳转回首页
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
            //视频截图上传成功回调
            handleHeadPicSuccess(data, file){
                if (data && data.code === 0) {
                    this.imageUrl = URL.createObjectURL(file.raw);
                    this.dataForm.headPic = data.fileUrl;
                    console.log(this.dataForm.headPic)
                    this.$message({
                        message: '头像上传成功',
                        type: 'success'
                    })
                } else {
                    this.$message.error(data.msg)
                }
            },
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
    .vp-form{
        position: relative;
        width: 80%;
        left: 10%;
    }
    .vp-textarea >>> .el-textarea__inner{
        height: 100px;
        width: 100%;
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
