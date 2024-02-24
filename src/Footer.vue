<template>
    <footer class="c-footer" id="c-footer" v-if="!isApp" :class="{ 'is--dark': darkMode }">
        <div class="c-footer-inner">
            <div class="c-footer-left">
                &copy;2024 <a href="https://www.jx3box.com/" class="u-beian" target="_blank">JX3BOX</a>.
                All rights reserved. Translated by <a href="https://vl3box.com/" class="u-beian" target="_blank">&copy;VL3BOX</a>.
            </div>
            <div class="c-footer-right">
                <a class="u-about" :href="about" target="_blank">{{ $t('关于我们') }}</a>
                <em>|</em>
                <a class="u-doc" href="/tool?subtype=4" target="_blank">{{ $t('帮助文档') }}</a>
                <em>|</em>
                <a class="u-log" href="https://github.com/JX3BOX" target="_blank">{{ $t('代码仓库') }}</a>
                <em>|</em>
                <a class="u-feedback" href="/feedback" target="_blank">{{ $t('反馈建议') }}</a>
            </div>
            <slot></slot>
        </div>
    </footer>
</template>

<script>
import { __Root, feedback } from "@jx3box/jx3box-common/data/jx3box.json";
import { isApp } from "../assets/js/app.js";
export default {
    name: "Footer",
    props: {
        darkMode: {
            type: Boolean,
            default: false,
        },
    },
    data: function () {
        return {
            about: __Root + "about",
            feedback: feedback + "&subject=" + location.href,
            isApp: isApp(),
        };
    },
};
</script>

<style lang="less">
@import "../assets/css/footer.less";
</style>
