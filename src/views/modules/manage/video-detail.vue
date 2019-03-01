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
                                    <el-input type="textarea" :row="4"  class="vp-textarea" v-model="commentContent"></el-input>
                                </el-col>
                                <el-col :span="3">
                                    <el-button class="vd-comment-btn" @click="submitComment">发表评论</el-button>
                                </el-col>
                            </el-row>
                        </el-col>
                    </el-row>
                    <!--评论列表-->
                    <el-row style="margin-top: 20px" v-for="item in commentList" :key="item.id" class="vd-comment-detail">
                        <!--头像区-->
                        <el-col :span="2">
                            <div>
                                <img class="vd-comment-head" v-if="item.commentPic" :src="$http.adornUrl(item.commentPic)">
                                <img class="vd-comment-head" v-else src="~@/assets/img/avatar.png">
                            </div>
                            <div>
                                {{item.commentName}}
                            </div>
                            </el-col>
                        <el-col :span="22" style="padding-top: 10px; padding-left: 10px">{{item.content}}</el-col>
                    </el-row>
                    <el-pagination
                        @size-change="sizeChangeHandle"
                        @current-change="currentChangeHandle"
                        :current-page="pageIndex"
                        :page-sizes="[10, 20, 50, 100]"
                        :page-size="pageSize"
                        :total="totalPage"
                        layout="total, sizes, prev, pager, next, jumper">
                    </el-pagination>
                </el-card>
            </el-col>
            <!--视频列表区-->
            <el-col :offset="1" :span="6">
                <el-card class="vd-author-box">
                    <el-row>
                        <el-col :span="6">
                            <img v-if="authorObj.headPic" :src="$http.adornUrl(authorObj.headPic)">
                            <img v-else src="~@/assets/img/avatar.png">
                        </el-col>
                        <el-col :span="18">
                            <div>
                                <span style="margin-right: 5px">{{authorObj.name}}</span>
                                <el-button size="small">发送消息</el-button>
                            </div>
                            <div style="margin-top: 5px">{{authorObj.remark}}</div>
                        </el-col>
                    </el-row>
                </el-card>
                <el-row v-for="item in videoList" :key="item.id" class="vd-video-item">
                    <el-col :span="8">
                        <img style="width: 90px;height: 90px;vertical-align: middle;margin-top: 5px; margin-left: 10px;cursor: pointer"
                             :src="$http.adornUrl(item.filePic)"
                             @click="gotoVideoDetail(item.id)">
                    </el-col>
                    <el-col :span="14" :offset="1">
                        <div style="padding: 14px 0">
                            <span style="font-weight: bold">{{item.name}}</span>
                        </div>
                        <div>{{item.remark}}</div>
                    </el-col>
                </el-row>
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
                videoObj: {},
                //评论内容
                commentContent:'',
                //评论列表
                commentList: [],
                pageIndex: 1,
                //最多显示15个最新数据
                pageSize: 15,
                totalPage: 0,
                dataListLoading: false,
                //作者信息
                authorObj:{},
                //视频列表
                videoList:{},
            }
        },
        components: {
        },
        computed: {
        },
        activated(){
            this.videoId = this.$route.query.id;
            this.getVideoById();
            this.getCommentList();
            this.getVideoList();
        },
        methods:{
            //根据视频ID获取视频内容
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
                        this.getAuthor(this.videoObj.petId);
                    } else {
                        this.$message.error(data.msg)
                    }
                })
            },
            //获取当前用户信息
            getAuthor(id){
                this.$http({
                    url: this.$http.adornUrl(`/manage/petInfo/infoByUserId`),
                    method: 'get',
                    params: this.$http.adornParams({
                        userId: id
                    })
                }).then(({data}) => {
                    if (data && data.code === 0) {
                        this.authorObj= data.info;
                    } else {
                        this.$message.error(data.msg)
                    }
                })
            },
            //提交评论
            submitComment(){
                this.$http({
                    url: this.$http.adornUrl(`/manage/videocomment/submitComment`),
                    method: 'post',
                    data: this.$http.adornData({
                        videoId: this.videoId,
                        content: this.commentContent

                    })
                }).then(({data}) => {
                    if (data && data.code === 0) {
                        this.$message.success("提交评论成功")
                        //清空评论栏
                        this.commentContent = '';
                        //刷新评论列表
                        this.getCommentList();
                    } else {
                        this.$message.error(data.msg)
                    }
                })
            },
            //获取评论列表
            getCommentList(){
                this.dataListLoading = true
                this.$http({
                    url: this.$http.adornUrl('/manage/videocomment/list'),
                    method: 'get',
                    params: this.$http.adornParams({
                        'page': this.pageIndex,
                        'limit': this.pageSize,
                        'videoId': this.videoId
                    })
                }).then(({data}) => {
                    if (data && data.code === 0) {
                        this.commentList = data.page.list
                        this.totalPage = data.page.totalCount
                    } else {
                        this.dataList = []
                        this.totalPage = 0
                    }
                    this.dataListLoading = false
                })
            },
            // 翻页--每页数量变化
            sizeChangeHandle (val) {
                this.pageSize = val
                this.pageIndex = 1
                this.getCommentList()
            },
            // 翻页--当前页
            currentChangeHandle (val) {
                this.pageIndex = val
                this.getCommentList()
            },
            //获取视频列表
            getVideoList(){
                this.$http({
                    url: this.$http.adornUrl('/manage/videoinfo/getVideoList'),
                    method: 'get',
                    params: this.$http.adornParams({
                        current: 1,
                        size: 20,
                        type: 0
                    })
                }).then(({data}) => {
                    if (data && data.code === 0) {
                        this.videoList = data.page.records;
                        //处理备注过长的情况
                        for(let i=0; i<this.videoList.length; i++){
                            if (this.videoList[i].remark.length > 44){
                                console.log(this.videoList[i].remark.length)
                                this.videoList[i].remark = this.videoList[i].remark.substring(0,44)+"......";
                            }
                        }
                    } else {
                        this.videoList = [];
                        this.$message.error(data.msg)
                    }
                })
            },
            //跳转到视频详情页
            gotoVideoDetail(id){
                this.$router.push({
                    path: '/manage/video-detail',
                    query:{
                        id: id
                    }
                })
                this.videoId = id;
                this.getVideoById();
                this.getCommentList();
                this.getVideoList();
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

    .vd-comment-detail{
        width: 100%;
        height: 120px;
        border-bottom: 1px solid #ebeef5;
    }

    .vd-comment-head{
        width: 50px;
        height: auto;
        margin-top: 10px;
        margin-right: 5px;
        margin-bottom: 8px;
        border-radius: 100%;
        vertical-align: middle;
    }

    /*作者信息栏样式*/
    .vd-author-box{
        width: 100%;
        height: 96px;
        border-bottom: 1px solid #ebeef5;
        border-top: 1px solid #ebeef5;
        margin-bottom: 20px;
        border-left: none;
        border-right: none;
    }

    .vd-author-box img{
        width: 60px;
        height: 60px;
        border-radius: 100%;
        vertical-align: middle;
    }

    .vd-video-item{
        width: 100%;
        height: 100px;
        margin-bottom: 10px;
    }

</style>
