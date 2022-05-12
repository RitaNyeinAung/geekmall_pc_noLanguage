<template>
    <div>
        <div class="invoice_info l">
            <ul class="top">
                <li @click="Hit(0)" :class="{ line: isLine == 0 }">
                    <p>电子发票</p>
                </li>
                <li @click="Hit(1)" :class="{ line: isLine == 1 }">
                    <p>纸质发票</p>
                </li>
            </ul>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            isLine: 0,
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
        Hit(index) {
            this.isLine = index;
        }
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
.invoice_info {
    height: 956px;
    width: 980px;
    background: #fff;
    margin: 16px 0;
    .top {
        overflow: hidden;
        margin: 14px 17px 20px;
        border-bottom: 1px solid #e7e7e7;
        height: 37px;
        li {
            float: left;
            cursor: pointer;
            height: 36px;
            color: #666;
            width: 122px;
            p {
                height: 16px;
                border-right: 1px solid #e7e7e7;
                text-align: center;
                margin-top: 10px;
                font-size: 12px;
                span {
                    color: #d02629;
                }
            }
        }
        li:nth-of-type(2) {
            width: 168px;
            p {
                border-right: 0;
            }
        }
        .line {
            color: #d02629;
            border-bottom: 1px solid #d02629;
        }
    }
}
</style>
