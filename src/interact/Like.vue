<template>
    <div v-if="mode == 'heart'" class="w-like-heart" @click="blast">
        <span class="w-heart" ref="likeheart"></span>
        <span class="u-text">{{ txt || "喜欢" }}</span>
        <span class="u-count" v-if="showCount">{{ total }}</span>
    </div>
    <el-button v-else class="w-like" size="mini" type="primary" @click="doLike">
        <i v-if="!status" class="u-heart">
            <img svg-inline src="../../assets/img/widget/heart.svg" />
        </i>
        <i v-else class="u-heart">
            <img svg-inline src="../../assets/img/widget/heart2.svg" />
        </i>
        <span>{{ $t('喜欢') }}</span>
    </el-button>
</template>

<script>
import { postStat } from "@jx3box/jx3box-common/js/stat";
import { getRewrite } from "@jx3box/jx3box-common/js/utils";
import _ from "lodash";
export default {
    name: "Like",
    props: [
        "mode",
        "txt",
        "showCount",
        "count",
        "postType",
        "postId",
    ],
    data: function () {
        return {
            pid:  this.postId || getRewrite("pid"),
            type: this.postType || location.pathname.split("/")[1],
            status: false,
            total: this.count || 0,
            clicks: 0,
        };
    },
    computed: {},
    watch: {
        count: function () {
            this.total = this.count || 0;
        },
    },
    methods: {
        addLike: function () {
            return (
                this.type && this.pid && postStat(this.type, this.pid, "likes")
            );
        },
        doLike: function () {
            this.status = !this.status;
            this.status && this.pid && this.addLike();
        },
        doLikes: _.throttle(function () {
            this.addLike();
        }, 2000),
        blast: function () {
            this.$refs.likeheart.classList.add("w-heart-animation");
            setTimeout(() => {
                this.$refs.likeheart.classList.remove("w-heart-animation");
            }, 800);

            this.total++;
            this.clicks++;
            this.doLikes();
        },
    },
    mounted: function () {},
};
</script>

<style lang="less">
@import "../../assets/css/like.less";
</style>
