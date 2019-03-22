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
                <el-main v-show="tabUser !=null && tabUser !== ''" id="chatContent">
                    <el-row v-for="item in chatRecordList" :key="item.id"
                            :class="userInfo.name===item.sendUser?'message-box-owner':'message-box-sender'" >
                        <div style="margin-bottom: 5px;margin-top: 5px;margin-left: 10px">
                            <span style="margin-right: 10px">{{item.sendUser}}</span>
                            <span>{{item.createTime}}</span>
                        </div>
                        <div>
                            {{item.content}}
                        </div>
                    </el-row>
                </el-main>
                <el-footer class="footer-st" height="120px" style="padding: 0" v-show="tabUser !=null && tabUser !== ''">
                    <el-input type="textarea" class="footer-textarea" height="80px" v-model="content"></el-input>
                    <el-button class="footer-btn" @click="messageSend">发送</el-button>
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
                tabUser:'',
                //消息框信息
                content:''
            }
        },
        activated(){
            this.getCurrentUser();
        },
        watch: {
            //设置滚动条自动到最下面
            chatRecordList() {
                this.$nextTick(() => {
                    let container = this.$el.querySelector("#chatContent");
                    console.log(container);
                    container.scrollTop = container.scrollHeight;
                })

            }
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
                //获取对应人员聊天记录
                this.getChatRecordList();
                console.log(index, keyPath)
            },
            //在指定对话框发送消息
            messageSend(){
                this.$http({
                    url: this.$http.adornUrl('/manage/chatRecord/save'),
                    method: 'post',
                    data: this.$http.adornData({
                        chatId: this.tabUser.id,
                        content: this.content,
                        sendUser: this.userInfo.name
                    })
                }).then(({data}) => {
                    if (data && data.code === 0) {
                        this.content='';
                        //更新聊天记录
                        this.getChatRecordList();
                    } else {
                        this.$message.error(data.msg)
                    }
                })
            },
            //获取聊天记录列表
            getChatRecordList(){
                this.$http({
                    url: this.$http.adornUrl('/manage/chatRecord/getChatRecordList'),
                    method: 'get',
                    params: this.$http.adornParams({
                        id: this.tabUser.id
                    })
                }).then(({data}) => {
                    if (data && data.code === 0) {
                        this.chatRecordList = data.list;
                    } else {
                        this.$message.error(data.msg)
                    }
                })
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
    /*聊天记录样式*/
    .message-box-owner{
        margin-bottom: 10px;
        min-height: 50px;
        width: 60%;
        position: relative;
        left: 40%;
        text-align:right;
        background-color: #6ce26c;
    }

    .message-box-sender{
        margin-bottom: 10px;
        min-height: 50px;
        width: 60%;
        background-color: #f4f4f4;
    }

</style>
