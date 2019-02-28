<template>
    <div>
        <el-row style="margin-top: 20px">
            <!--视频区-->
            <el-col :span="14" :offset="2">
                <el-card style="border: none;">
                    <div slot="header" class="clearfix">
                        <el-row>
                            <el-col :span="4" :offset="10">
                                <h2 style="width: 200px;">{{videoObj.name}}</h2>
                            </el-col>
                        </el-row>
                    </div>
                    <video v-if="videoSrc" :src="videoSrc" :poster="headSrc" controls="controls" class="vd-video"></video>
                </el-card>
                <!--视频描述区-->
                <el-card class="vd-content-card">
                    <p>{{videoObj.remark}}</p>
                </el-card>
                <!--评论区-->
                <el-card class="vd-comment-card">
                    <!--评论提交区-->
                    <el-row>
                        <el-col :span="22" :offset="2">
                            <el-row>
                                <el-col :span="21">
                                    <el-input type="textarea" :row="4"  class="vp-textarea"></el-input>
                                </el-col>
                                <el-col :span="3">
                                    <el-button class="vd-comment-btn">发表评论</el-button>
                                </el-col>
                            </el-row>
                        </el-col>
                    </el-row>
                    <!--评论列表-->
                    <el-row style="margin-top: 20px">
                        <!--头像区-->
                        <el-col :span="2"></el-col>
                        <el-col :span="22"></el-col>
                    </el-row>
                </el-card>
            </el-col>
        </el-row>
    </div>
</template>

<script>
    export default {
        name: "video-detail",
        data(){
            return {
                videoId: '',
                //视频路径地址
                videoSrc: '',
                //视频截屏地址
                headSrc: '',
                //视频结构体
                videoObj: {}
            }
        },
        components: {
        },
        computed: {
        },
        activated(){
            this.videoId = this.$route.query.id;
            this.getVideoById();
        },
        methods:{
            getVideoById(){
                if (this.videoId == null){
                    this.$message.error("id无效，请后退后重试")
                    return;
                }
                this.$http({
                    url: this.$http.adornUrl(`/manage/videoinfo/info/${this.videoId}`),
                    method: 'get',
                    params: this.$http.adornParams({})
                }).then(({data}) => {
                    if (data && data.code === 0) {
                        this.videoObj = data.videoInfo;
                        this.videoSrc = this.$http.adornUrl(this.videoObj.fileUrl)
                        this.headSrc = this.$http.adornUrl(this.videoObj.filePic)
                    } else {
                        this.$message.error(data.msg)
                    }
                })
            },
        }
    }
</script>

<style scoped>
    .vd-video{
        display: block;
        width: 1024px;
        height: 800px;
        object-fit: fill;
    }

    .vp-textarea >>> .el-textarea__inner{
        height: 120px;
        width: 100%;
    }
    .vd-comment-btn{
        margin-left: 10px;
        padding: 5px 10px;
        text-align: center;
        color: #fff;
        height: 120px;
        font-size: 18px;
        width: 100%;
        background-color: #00b5e5;
    }
    .vd-content-card{
        height: 100px;
        width: 90%;
        position: relative;
        left: 5%;
        border-bottom: 1px solid #ebeef5;
        border-top: 1px solid #ebeef5;
        border-left: none;
        border-right: none;
    }

    .vd-comment-card{
        width: 100%;
        border-bottom: 1px solid #ebeef5;
        border-top: 1px solid #ebeef5;
        border-left: none;
        border-right: none;
    }


</style>
