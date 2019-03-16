<template>
    <nav class="site-navbar" :class="'site-navbar--' + navbarLayoutType">
        <div class="site-navbar__header">
            <h1 class="site-navbar__brand" @click="$router.push({ name: 'home' })">
                <a class="site-navbar__brand-lg" href="javascript:;">宠物特搜站</a>
                <!--<a class="site-navbar__brand-mini" href="javascript:;">人人</a>-->
            </h1>
        </div>
        <div class="site-navbar__body clearfix">
            <el-menu
                class="site-navbar__menu site-navbar__menu--right" mode="horizontal">

                <el-menu-item index="1" @click="$router.push({ name: 'home' })">首页</el-menu-item>
                <el-submenu index="2">
                    <template slot="title">观看历史</template>
                    <el-menu-item v-for="item in playRecords" :key="item.id" :index="item.id.toString()">
                        <a @click="gotoVideoDetail(item.videoId)">{{item.name}}</a>
                    </el-menu-item>
                </el-submenu>
                <el-menu-item index="3" @click="$router.push({ name: 'manage-video-public' })">发布视频</el-menu-item>
                <el-menu-item index="5" @click="$router.push({ name: 'manage-pet-info' })">个人中心</el-menu-item>
                <el-menu-item class="site-navbar__avatar" index="5">
                    <el-dropdown :show-timeout="0" placement="bottom">
            <span class="el-dropdown-link">
              <img src="~@/assets/img/avatar.png" :alt="userName">{{ userName }}
            </span>
                        <el-dropdown-menu slot="dropdown">
                            <el-dropdown-item @click.native="updatePasswordHandle()">修改密码</el-dropdown-item>
                            <el-dropdown-item @click.native="logoutHandle()">退出</el-dropdown-item>
                        </el-dropdown-menu>
                    </el-dropdown>
                </el-menu-item>
            </el-menu>
        </div>
        <!-- 弹窗, 修改密码 -->
        <update-password v-if="updatePassowrdVisible" ref="updatePassowrd"></update-password>
    </nav>
</template>

<script>
    import UpdatePassword from './main-navbar-update-password'
    import { clearLoginInfo } from '@/utils'
    export default {
        data () {
            return {
                updatePassowrdVisible: false,
                //播放记录列表
                playRecords: [],
            }
        },
        components: {
            UpdatePassword
        },
        mounted(){
            this.getPlayRecord();
        },
        computed: {
            navbarLayoutType: {
                get () { return this.$store.state.common.navbarLayoutType }
            },
            sidebarFold: {
                get () { return this.$store.state.common.sidebarFold },
                set (val) { this.$store.commit('common/updateSidebarFold', val) }
            },
            mainTabs: {
                get () { return this.$store.state.common.mainTabs },
                set (val) { this.$store.commit('common/updateMainTabs', val) }
            },
            userName: {
                get () { return this.$store.state.user.name }
            }
        },
        methods: {
            //获取当前用户的最近五条观看记录
            getPlayRecord(){
                this.$http({
                    url: this.$http.adornUrl('/manage/playrecord/getRecordsLimit'),
                    method: 'get',
                    param: this.$http.adornParams({})
                }).then(({data}) => {
                    if (data && data.code === 0) {
                        this.playRecords = data.list;
                    }
                })
            },
            // 修改密码
            updatePasswordHandle () {
                this.updatePassowrdVisible = true
                this.$nextTick(() => {
                    this.$refs.updatePassowrd.init()
                })
            },
            // 退出
            logoutHandle () {
                this.$confirm(`确定进行[退出]操作?`, '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {
                    this.$http({
                        url: this.$http.adornUrl('/sys/logout'),
                        method: 'post',
                        data: this.$http.adornData()
                    }).then(({data}) => {
                        if (data && data.code === 0) {
                            clearLoginInfo()
                            this.$router.push({ name: 'login' })
                        }
                    })
                }).catch(() => {})
            },
            //跳转到视频详情页
            gotoVideoDetail(id){
                this.$router.push({
                    path: '/manage/video-detail',
                    query:{
                        id: id
                    }
                });
                window.location.reload();
                this.getPlayRecord();
            },
        }
    }
</script>
