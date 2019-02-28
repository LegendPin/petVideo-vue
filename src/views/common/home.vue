<template>
    <div class="mod-home">
        <el-tabs v-model="activeTab" @tab-click="tabChange">
            <el-tab-pane label="全部" name="0">
                <el-row>
                    <el-col :span="3" v-for="(o, index) in videoList" :key="o.id" :offset="index > 0 ? 1 : 0">
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
            <el-tab-pane
                v-for="item in tabList"
                :key="item.dictCode"
                :label="item.dictName"
                :name="item.dictCode.toString()">
                <el-row>
                    <el-col :span="3" v-for="(o, index) in videoList" :key="o.id" :offset="index > 0 ? 1 : 0">
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
        </el-tabs>
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
                totalPage: 0
            }
        },
        activated(){
            this.getDictListByParent();
            this.getVideoList();
        },
        methods:{
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
                this.getVideoList()
            },
            // 当前页
            currentChangeHandle (val) {
                this.current = val
                this.getVideoList()
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
            //标签页改变事件
            tabChange(){
                console.log(this.activeTab)
                this.getVideoList();
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

