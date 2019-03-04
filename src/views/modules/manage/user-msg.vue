<template>
        <el-container class="center-box">
            <el-aside width="200px" style="background-color: rgb(238, 241, 246)">
                <el-menu>
                    <el-menu-item index="1">选项1</el-menu-item>
                    <el-menu-item index="2">选项2</el-menu-item>
                    <el-menu-item index="3">选项3</el-menu-item>
                </el-menu>
            </el-aside>
            <el-container>
                <el-header style="text-align: center; font-size: 16px;font-weight: bold;height: 50px">
                    <span>王小虎</span>
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
        name: "user-msg"
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
