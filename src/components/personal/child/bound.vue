<template>
    <div class="bound l">
        <div class="top">
            <span>账户绑定</span
            >（绑定第三方账户，绑定成功后即可使用第三方账户快速登录）
        </div>
        <div class="down">
            <p class="l"><img src="../../../assets/img/qq.png" />QQ账户</p>
            <span class="l"
                >您还没有绑定qq账户，绑定后您可以使用qq快速登录。</span
            >
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
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
    mounted(){
        var self = this
            window.setTimeout(function () {
                self.headParams.title = sessionStorage.titleKey
                self.headParams.description = sessionStorage.updateDescription
                self.headParams.keywords = sessionStorage.contentKey
                self.headParams.link = sessionStorage.pcfavicon
                self.$emit('updateHead')
        }, 3000)
    },
    created() {
        if(localStorage.getItem("loginuserdata") == 'true') {
                let title = sessionStorage.getItem('titleKey') + '-' +sessionStorage.getItem('updateDescription');
            this.showScroll.scrollTitle(title);
        } else {
            this.$router.push('/passwordLogin')
        }        
      this.getFootData();
      this.getFavIcon(); 
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
.bound {
    height: 954px;
    width: 980px;
    background: #fff;
    margin-top: 16px;
    .top {
        height: 37px;
        border-bottom: 1px solid #e7e7e7;
        margin: 14px 17px 0;
        font-size: 12px;
        color: #b6b6b6;
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
    .down {
        height: 93px;
        border: 1px solid #e7e7e7;
        margin: 20px 17px;
        p {
            height: 44px;
            width: 194px;
            border-right: 1px solid #e7e7e7;
            font-size: 14px;
            color: #333;
            line-height: 44px;
            margin-top: 27px;
            img {
                float: left;
                margin: -5px 11px 0 17px;
            }
        }
        span {
            font-size: 12px;
            color: #a0a0a0;
            margin: 29px 0 0 25px;
        }
    }
}
</style>
