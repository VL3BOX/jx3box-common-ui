<template>
    <div class="w-creators" v-if="visible">
        <span class="w-creators-title">{{ $t('联合创作') }}</span>
        <a
            class="w-creators-super w-creators-item"
            v-if="super_author"
            :href="authorLink(super_author.ID)"
            target="_blank"
        >
            <img class="u-avatar" :src="showAvatar(super_author.user_avatar)" />
            <span class="u-name">{{ super_author.display_name }}</span>
            <i class="u-up">UP</i>
        </a>
        <div class="w-creators-authors" v-if="other_authors">
            <a
                class="w-creators-author w-creators-item"
                v-for="(item, i) in other_authors"
                :key="i"
                :href="authorLink(item.post_author_info.ID)"
                target="_blank"
                v-show="item.status"
            >
                <img class="u-avatar" :src="showAvatar(item.post_author_info.user_avatar)" />
                <span class="u-name">{{ item.post_author_info.display_name }}</span>
                <i class="u-label">{{ formatLabel(item.label) }}</i>
            </a>
        </div>
        <a class="w-creators-edit" :href="editLink" v-if="isCreator">
            <i class="el-icon-edit-outline"></i> 编辑当前作品
        </a>
    </div>
</template>

<script>
import { getPostAuthors } from "../../service/cms";
import { showAvatar, authorLink, editLink } from "@jx3box/jx3box-common/js/utils";
import User from "@jx3box/jx3box-common/js/user";
export default {
    name: "Creators",
    props: ["postId", "postType"],
    components: {},
    data: function () {
        return {
            super_author: "",
            other_authors: "",
        };
    },
    computed: {
        id: function () {
            return this.postId;
        },
        visible: function () {
            return this.other_authors && this.other_authors?.length;
        },
        editLink: function () {
            return editLink(this.postType, this.postId);
        },
        creators: function ({ other_authors }) {
            return other_authors.map((item) => {
                return item.author_id;
            });
        },
        userId: function () {
            return ~~User.getInfo().uid;
        },
        isCreator: function ({ creators, userId, super_author }) {
            return creators.includes(userId) || userId == super_author.ID;
        },
    },
    watch: {
        id: {
            immediate: true,
            handler: function (val) {
                val && this.loadData();
            },
        },
    },
    methods: {
        loadData: function () {
            getPostAuthors(this.id).then((res) => {
                this.super_author = res.data?.data.super_author;
                this.other_authors = res.data?.data.other_authors;

                const super_author = {
                    user_id: this.super_author.ID,
                    display_name: this.super_author.display_name,
                    user_avatar: this.super_author.user_avatar,
                };

                const other_authors = this.other_authors?.map((item) => {
                    if (item.status) {
                        return {
                            user_id: item.post_author_info?.ID,
                            display_name: item.post_author_info?.display_name,
                            user_avatar: item.post_author_info?.user_avatar,
                        };
                    }
                }).filter((item) => {
                    return !!item;
                });

                // 将联合创作者传出去
                this.$emit("load-authors", { super_author, other_authors });
            });
        },
        showAvatar: function (val) {
            return showAvatar(val, 144);
        },
        authorLink,
        formatLabel: function (str) {
            return str && str.slice(0, 8);
        },
    },
    created: function () {},
    mounted: function () {},
};
</script>

<style lang="less">
.w-creators {
    display: flex;
    border: 1px solid #ddd;
    background-color: #fafbfc;
    border-radius: 4px;
    .pr;
    overflow: hidden;
    padding: 15px 10px 15px 40px;
    overflow-x: auto;
    .clearfix;
}
.w-creators-title {
    .pa;
    .lt(0);
    writing-mode: vertical-rl;
    .fz(12px);
    background: @primary;
    color: #fff;
    .h(100%);
    .x;
    padding: 0 3px;
    letter-spacing: 1px;
}
.w-creators-super {
    // .fl;
}
.w-creators-authors {
    // .fl;
    display: flex;
    flex-grow: 1;
}
.w-creators-item {
    flex-shrink: 0;
    .pr;
    .u-avatar {
        .size(48px);
        .pa;
        .lt(0);
        .r(4px);
    }
    .pl(58px);
    .mr(20px);
    .u-name {
        .fz(15px);
        .bold;
        .db;
        color: @color;
        margin-bottom: 2px;
        .nobreak;
        max-width: 120px;
    }
    &:hover {
        .u-name {
            color: @color-link;
        }
    }
    .u-up {
        .dbi;
        .fz(12px,1);
        font-style: normal;
        border: 1px solid @pink;
        color: @pink;
        padding: 3px 10px;
        .r(2px);
        .mt(5px);
    }
    .u-label {
        .nobreak;
        max-width: 120px;
        .dbi;
        .fz(12px,1);
        font-style: normal;
        border: 1px solid @color-link;
        color: @color-link;
        padding: 3px 10px;
        .r(2px);
        .mt(5px);
    }
}
.w-creators-edit {
    .pa;
    .rb(10px);
    .fz(13px);
    background-color: #e6f0fb;
    padding: 2px 5px;
    &:hover {
        color: @pink;
    }
}
@media screen and (max-width: @phone) {
    .w-creators-edit {
        .none;
    }
}
</style>
