<template>
    <div class="mineNews l">
        <div class="top"><span>我的消息</span></div>
        <div class="same" v-for="(item, index) in messageData" :key="item.id">
            <p>
                <span class="l"></span>{{ item.theme
                }}<span class="r" v-if="item.create_time">{{
                    item.create_time | formatDate
                }}</span>
            </p>
            <div class="check_status">
                <span>{{ item.news_info }}</span>
                <el-button size="mini" v-if="item.status == 0" @click="handleCheck(item.id)">查看</el-button>
                <el-button size="mini" v-if="item.status == 1" :disabled='true'
                    >已查看</el-button
                >
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            messageData: {},
            headParams: {
                title: sessionStorage.getItem('titleKey'),
                description: sessionStorage.getItem('updateDescription'),
                keywords: sessionStorage.getItem('contentKey'),
                link:sessionStorage.getItem('pcfavicon')         
            }
        };
    },
    head: {
        meta: function(){
            return [
                { name: 'title', content: this.headParams.title, id: 'desc' },
                { name: 'description', content: this.headParams.description, id: 'desc1' },
                { name: 'keywords', content: this.headParams.keywords, id: 'desc2' },
            ]
        },
        link: function(){
          return [
            { rel: 'shortcut icon', href: 'imgRequest'+this.headParams.link, id:'pcLink'},
          ]  
        }
    },

    created() {
        this.getMessageInfo();
        this.getFootData();
        this.getFavIcon(); 
    },
    mounted() {
        var self = this
            window.setTimeout(function () {
                self.headParams.title = sessionStorage.titleKey
                self.headParams.description = sessionStorage.updateDescription
                self.headParams.keywords = sessionStorage.contentKey
                self.headParams.link = sessionStorage.pcfavicon
                self.$emit('updateHead')
        }, 3000)
    },
    methods: {
        getFootData() {
            this.HTTP(this.$httpConfig.aboutEtcetera, {}, "post")
                .then(res => {
                    sessionStorage.setItem(
                        "titleKey",
                        res.data.data.intnet_title
                    );
                    sessionStorage.setItem("updateDescription", res.data.data.intnet_description);
                    sessionStorage.setItem("contentKey", res.data.data.init_key_word);
                        let title=sessionStorage.getItem('titleKey') + '-' +sessionStorage.getItem('updateDescription');
                        this.showScroll.scrollTitle(title);
                })
                .catch(err => {
                    console.log(err);
                });
        },
        getFavIcon() {
            this.HTTP(this.$httpConfig.getFavIcon, {}, "post")
                .then(res => {
                    sessionStorage.setItem("pcfavicon", res.data.data.favicon);
                })
                .catch(err => {
                    console.log(err);
                });
        },
        getMessageInfo() {
            this.HTTP(this.$httpConfig.messageList, {})
                .then(res => {
                    this.messageData = res.data.data.data;
                })
                .catch(err => {
                    console.error(err);
                });
        },
        handleCheck(id){
             this.HTTP(this.$httpConfig.messageCheck,{
                 id:id
             }).then(res => {
            });
            this.getMessageInfo()
        },
    }
};
</script>

<style lang="less" scoped>
.l {
    float: left;
}
.r {
    float: right;
}
.center {
    width: 1200px;
    margin: 0 auto;
    height: 100%;
}
.mineNews {
    height: 954px;
    width: 980px;
    background: #fff;
    margin-top: 16px;
    .top {
        height: 37px;
        border-bottom: 1px solid #e7e7e7;
        margin: 14px 17px 0;
        span {
            display: inline-block;
            width: 104px;
            text-align: center;
            line-height: 35px;
            border-bottom: 2px solid #d02629;
            color: #d02629;
            font-size: 14px;
        }
    }
    .same {
        overflow: hidden;
        margin: 0 17px;
        border-bottom: 1px dashed #e7e7e7;
        .check_status {
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
        p {
            font-size: 14px;
            color: #333;
            line-height: 48px;
            span.r {
                color: #666;
                font-size: 10px;
            }
            span.l {
                width: 4px;
                height: 4px;
                background: #d29d2b;
                margin: 23px 10px 0 0;
            }
        }
        span {
            font-size: 12px;
            color: #555;
            line-height: 24px;
            margin: 12px 0;
            display: block;
        }
    }
}
</style>
