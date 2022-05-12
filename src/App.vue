<template>
    <div id="web" v-show="$constant.copyright">
        <router-view :key="$route.path + $route.query.id"></router-view>
    </div>
</template>
<script>
import $ from "jquery";
export default {
    data() {
        return {};
    },
    created() {
        this.registerMethod();
    },
    methods: {
        registerMethod() {
            this.HTTP(this.$httpConfig.getLoginMethod, {}, "post")
            .then(res => {
            this.$store.state.loginMethod = res.data.data;
            })
            .catch(e => {
            console.log(e);
            });
        }
    },
    mounted() {}
};
</script>
<style>
html,
body {
    width: 100%;
    height: 100%;
}
</style>

<style lang="less" scoped>
#web {
    width: 100%;
    height: 100%;
}
</style>
