<template>
    <transition name='slideIn' mode='in-out'>
        <div id="index">

            <template v-if="!advertisement">
                <!-- 视图 -->
                <transition name='fade'>
                    <keep-alive>
                        <router-view></router-view>
                    </keep-alive>
                </transition>
                <!-- 底部导航栏 -->
                <nav-bar></nav-bar>
            </template>

            <!-- 广告页 -->
            <div class="coverAd" v-if="advertisement">
                <img class="coverOne"  v-if="coverImg" :src="coverImg">
                <img src="~@/assets/icon/1.png">
                <button @click.stop="advertisement = false">
                    <span>跳过</span>
                </button>
            </div>

        </div>
    </transition>
</template>
<script>
import navBar from './navBar'
import { mapActions } from 'vuex'
import { versionUpdate } from '@/config/cordova'
export default {
    components: { navBar },
    data() {
        return {
            advertisement: false,   // 广告
            time: 3,                // 广告倒计时
            coverImg: ''            // 广告图片
        }
    },
    methods: {
        ...mapActions('index', [
            'get_coverImg_data'     // 获取广告图片
        ]),
        // 广告初始化
        addvertisement_init() {
            // 如果网络畅通，请求广告图片，否则直接进入主页
            if (navigator.onLine) {
                this.advertisement = true
                this.get_coverImg_data()
                .then(res => {
                    if (res) {
                        this.coverImg = res.data
                    }
                })
                // 广告计时器
                let timeOut = setInterval(() => {
                        this.time--
                        if (this.time < 0) {
                            this.advertisement = false
                            clearInterval(timeOut)
                        }
                }, 1000)
            } else {
                this.advertisement = false
            }
        }
    },
    created() {
        this.addvertisement_init()   // 广告初始化
        versionUpdate()              // 版本更新
    }
}
</script>
<style lang='stylus'>
#index {
    width: 100%;
    height: 100%;
    overflow: hidden;
    .coverAd {
        width: 100%;
        height: 100%;
        position: fixed;
        top: 0;
        left: 0;
        z-index: 999;
        .coverOne {
            width: 100%;
            height: 80%;
            position: absolute;
            top: 0;
            left: 0;
            z-index: 999;
        }
        img {
            width: 100%;
            height: 100%;
        }
        button {
            position: absolute;
            width: 55px;
            height: 26px;
            top: 75%;
            right: 10px;
            background: #333;
            border-radius: 5px;
            -moz-opacity: 0.7;
            opacity: 0.7;
            margin: 0 auto;
            border: 1px solid #e2e2e2;
            z-index: 1000;
            span {
                color: #ffffff;
                width: 55px;
                height: 24px;
                margin: 0 auto;
                text-align: center;
                font-size 12px;
                display: table-cell;
                vertical-align: middle;
            }
        }
    }
}
</style>
