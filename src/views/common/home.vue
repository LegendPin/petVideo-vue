<template>
    <div class="mod-home">
        <el-tabs v-mode="activeTab">
            <el-tab-pane label="全部" name="">
                <el-row>
                    <el-col :span="3" v-for="(o, index) in 2" :key="o" :offset="index > 0 ? 1 : 0">
                        <el-card :body-style="{ padding: '0px' }">
                            <img class="image">
                            <div style="padding: 14px;">
                                <span>好吃的汉堡</span>
                                <div class="bottom clearfix">
                                    <time class="time">{{ currentDate }}</time>
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
                :name="item.dictName">
                <el-row>
                    <el-col :span="3" v-for="(o, index) in 2" :key="o" :offset="index > 0 ? 1 : 0">
                        <el-card :body-style="{ padding: '0px' }">
                            <img class="image">
                            <div style="padding: 14px;">
                                <span>好吃的汉堡</span>
                                <div class="bottom clearfix">
                                    <time class="time">{{ currentDate }}</time>
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
                activeTab: ''
            }
        },
        activated(){
            this.getDictListByParent();
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

