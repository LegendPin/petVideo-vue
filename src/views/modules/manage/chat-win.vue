<template>
        <el-container class="center-box">
            <el-aside width="200px" style="background-color: rgb(238, 241, 246)">
                <el-menu unique-opened @select="handleChatSelected">
                    <el-menu-item v-for="(item,index) in chatList"
                                  :index="index.toString()" :key="index">
                        <span v-if="item.onePet === userInfo.id">{{item.secondName}}</span>
                        <span v-else>{{item.oneName}}</span>
                    </el-menu-item>
                </el-menu>
            </el-aside>
            <el-container>
                <el-header style="text-align: center; font-size: 16px;font-weight: bold;height: 50px">
                    <span v-if="tabUser.onePet=== userInfo.id">{{tabUser.secondName}}</span>
                    <span v-else>{{tabUser.oneName}}</span>
                </el-header>
                <el-main>

                </el-main>
                <el-footer class="footer-st" height="120px" style="padding: 0">
                    <el-input type="textarea" class="footer-textarea" height="80px"></el-input>
                    <el-button class="footer-btn">发送</el-button>
                </el-footer>
            </el-container>
        </el-container>
</template>

<script>
    export default {
        name: "chat-win",
        data(){
            return {
                //聊天用户列表
                chatList: [],
                //聊天记录列表
                chatRecordList:[],
                //当前用户信息
                userInfo:[],
                //当前正在聊天的用户
                tabUser:''
            }
        },
        activated(){
            this.getCurrentUser();
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
                        this.getChatUserList();
                    } else {
                        this.$message.error(data.msg)
                    }
                })
            },
            //查询当前用户的聊天列表
            getChatUserList(){
                this.$http({
                    url: this.$http.adornUrl('/manage/petChat/getChatUserList'),
                    method: 'get',
                    params: this.$http.adornParams({
                        id: this.userInfo.id
                    })
                }).then(({data}) => {
                    if (data && data.code === 0) {
                        this.chatList = data.list;
                    } else {
                        this.$message.error(data.msg)
                    }
                })
            },
            //聊天人员选中事件
            handleChatSelected(index, keyPath){
                this.tabUser = this.chatList[parseInt(index)];
                console.log(index, keyPath)
            }
        }

    }
</script>

<style scoped>
    .center-box{
        position: relative;
        left: 20%;
        top: 25px;
        width: 60%;
        height: 650px;
        border: 1px #eee solid;
    }
    .el-header {
        background-color: #B3C0D1;
        color: #333;
        line-height: 50px;
    }
    .el-aside {
        color: #333;
    }

    .footer-textarea>>> .el-textarea__inner{
        height: 80px;
        width: 100%;
    }
    .footer-btn{
        float: right;
        margin-top: 5px;
        margin-right: 10px;
        text-align: center;
        width: 80px;
        height: 30px;
    }

</style>
