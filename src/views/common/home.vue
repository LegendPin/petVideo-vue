<template>
    <div class="mod-home">
        <el-tabs v-model="activeTab" @tab-click="tabChange">
            <el-tab-pane label="全部" name="0">
                <el-row>
                    <el-col :span="3" v-for="(o, index) in videoList" :key="o.id" :offset="1">
                        <el-card :body-style="{ padding: '0px' }" >
                            <img class="image" :src="$http.adornUrl(o.filePic)" @click="gotoVideoDetail(o.id)">
                            <div style="padding: 14px;">
                                <span>{{o.name}}</span>
                                <div class="bottom clearfix">
                                    <time class="time">{{ o.createTime }}</time>
                                </div>
                            </div>
                        </el-card>
                    </el-col>
                </el-row>
            </el-tab-pane>
            <el-tab-pane
                v-for="item in tabList"
                :key="item.dictCode"
                :label="item.dictName"
                :name="item.dictCode.toString()">
                <el-row>
                    <el-col :span="3" v-for="(o, index) in videoList" :key="o.id" :offset="1">
                        <el-card :body-style="{ padding: '0px' }">
                            <img class="image" :src="$http.adornUrl(o.filePic)" @click="gotoVideoDetail(o.id)">
                            <div style="padding: 14px;">
                                <span>{{o.name}}</span>
                                <div class="bottom clearfix">
                                    <time class="time">{{ o.createTime }}</time>
                                </div>
                            </div>
                        </el-card>
                    </el-col>
                </el-row>
            </el-tab-pane>
            <el-tab-pane label="个人视频" name="9999">
                <el-row>
                    <el-col :span="3" v-for="(o, index) in videoList" :key="o.id" :offset="1">
                        <el-card :body-style="{ padding: '0px' }" >
                            <img class="image" :src="$http.adornUrl(o.filePic)" @click="gotoVideoEdit(o.id)">
                            <div style="padding: 14px;">
                                <span>{{o.name}}</span>
                                <el-button style="float: right;line-height: 0.3;" type="danger" size="mini" @click="deleteVideo(o.id)">删除</el-button>
                                <div class="bottom clearfix">
                                    <time class="time">{{ o.createTime }}</time>
                                </div>
                            </div>
                        </el-card>
                    </el-col>
                </el-row>
            </el-tab-pane>
        </el-tabs>
        <!--翻页控件，只有当总数大于100时才会出现-->
        <el-pagination
            v-if="totalPage > 100"
            @size-change="sizeChangeHandle"
            @current-change="currentChangeHandle"
            :current-page="current"
            :page-sizes="[10, 20, 50, 100]"
            :page-size="size"
            :total="totalPage"
            layout="total, sizes, prev, pager, next, jumper">
        </el-pagination>
    </div>
</template>

<script>
    export default {
        data(){
            return {
                tabList: [],
                videoList:[],
                activeTab: 0,
                current: 1,
                size: 100,
                totalPage: 0,
                //当前用户信息
                userInfo:{}
            }
        },
        activated(){
            this.getCurrentUser();
            this.getDictListByParent();
            this.tabChange();
        },
        methods:{
            //获取当前用户信息
            getCurrentUser(){
                this.$http({
                    url: this.$http.adornUrl(`/manage/petInfo/info`),
                    method: 'get',
                    params: this.$http.adornParams({})
                }).then(({data}) => {
                    if (data && data.code === 0) {
                        this.userInfo= data.info;
                    } else {
                        this.$message.error(data.msg)
                    }
                })
            },
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
            //获取视频列表
            getVideoList(){
                this.$http({
                    url: this.$http.adornUrl('/manage/videoinfo/getVideoList'),
                    method: 'get',
                    params: this.$http.adornParams({
                        current: this.current,
                        size: this.size,
                        type: this.activeTab
                    })
                }).then(({data}) => {
                    if (data && data.code === 0) {
                        this.videoList = data.page.records;
                        this.totalPage = data.page.total
                    } else {
                        this.videoList = [];
                        this.totalPage = 0
                        this.$message.error(data.msg)
                    }
                })
            },
            // 每页数
            sizeChangeHandle (val) {
                this.size = val
                this.current = 1
                this.tabChange()
            },
            // 当前页
            currentChangeHandle (val) {
                this.current = val
                this.tabChange()
            },
            //跳转到视频详情页
            gotoVideoDetail(id){
                this.$router.push({
                    path: '/manage/video-detail',
                    query:{
                        id: id
                    }
                })
            },
            //获取当前用户的视频列表
            getCurrentUserVideos(){
                this.$http({
                    url: this.$http.adornUrl('/manage/videoinfo/getVideoList'),
                    method: 'get',
                    params: this.$http.adornParams({
                        current: this.current,
                        size: this.size,
                        petId: this.userInfo.id
                    })
                }).then(({data}) => {
                    if (data && data.code === 0) {
                        this.videoList = data.page.records;
                        this.totalPage = data.page.total
                    } else {
                        this.videoList = [];
                        this.totalPage = 0
                        this.$message.error(data.msg)
                    }
                })
            },
            //标签页改变事件
            tabChange(){
                console.log(this.activeTab)
                if (this.activeTab == '9999'){
                    this.getCurrentUserVideos();
                } else {
                    this.getVideoList();
                }

            },
            //进入视频编辑页面
            gotoVideoEdit(id){
                console.log(id)
                this.$router.push({
                    path: '/manage/video-edit',
                    query:{
                        id: id
                    }
                })
            },
            //删除视频
            deleteVideo(id){
                //封装数组型ID
                let ids = [id];
                this.$confirm(`确认删除该视频？`, '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {
                    this.$http({
                        url: this.$http.adornUrl('/manage/videoinfo/delete'),
                        method: 'post',
                        data: this.$http.adornData(ids, false)
                    }).then(({data}) => {
                        if (data && data.code === 0) {
                            this.$message({
                                message: '删除成功',
                                type: 'success',
                                duration: 1000,
                                onClose: () => {
                                    this.getCurrentUserVideos()
                                }
                            })
                        } else {
                            this.$message.error(data.msg)
                        }
                    })
                })
            }
        }
    }
</script>

<style>
    .mod-home {
        line-height: 1.5;
    }

    .time {
        font-size: 13px;
        color: #999;
    }

    .bottom {
        margin-top: 13px;
        line-height: 12px;
    }

    .button {
        padding: 0;
        float: right;
    }

    .image {
        width: 100%;
        height: 180px;
        display: block;
        cursor: pointer;
    }

    .clearfix:before,
    .clearfix:after {
        display: table;
        content: "";
    }

    .clearfix:after {
        clear: both
    }
</style>

