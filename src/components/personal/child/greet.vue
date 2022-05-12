<template>
    <div class="greet">
        <div class="l middle">
            <div class="up">
                <div class="l">
                    <!-- <img
                        class="l"
                        :src="URL + userInfo.user_header"
                        v-if="userInfo.user_header"
                    /><img
                        v-else
                        class="l"
                        src="../../../assets/img/default-head.png"
                    /> -->
                    <div v-if="userInfo.user_header">
                        <img
                            class="l"
                            :src="URL + userInfo.user_header"
                            v-if="userInfo.user_header.split(':').length == 1"
                            onerror="this.src='https://avataaars.io/?avatarStyle=Circle&topType=ShortHairShortRound&accessoriesType=Blank&hairColor=Black&facialHairType=Blank&clotheType=BlazerShirt&eyeType=WinkWacky&eyebrowType=Default&mouthType=Default&skinColor=Light'"
                        />
                        <img
                            class="l"
                            :src="userInfo.user_header"
                            v-if="userInfo.user_header.split(':').length == 2"
                            onerror="this.src='https://avataaars.io/?avatarStyle=Circle&topType=ShortHairShortRound&accessoriesType=Blank&hairColor=Black&facialHairType=Blank&clotheType=BlazerShirt&eyeType=WinkWacky&eyebrowType=Default&mouthType=Default&skinColor=Light'"
                        />
                    </div>
                    <div v-else>
                        <img
                            class="l" 
                            src="https://avataaars.io/?avatarStyle=Circle&topType=ShortHairShortRound&accessoriesType=Blank&hairColor=Black&facialHairType=Blank&clotheType=BlazerShirt&eyeType=WinkWacky&eyebrowType=Default&mouthType=Default&skinColor=Light" 
                            onerror="this.src='https://avataaars.io/?avatarStyle=Circle&topType=ShortHairShortRound&accessoriesType=Blank&hairColor=Black&facialHairType=Blank&clotheType=BlazerShirt&eyeType=WinkWacky&eyebrowType=Default&mouthType=Default&skinColor=Light'"/>
                    </div>
                    <p class="l">
                        欢迎{{ user_name }}进入{{
                            this.$constant.mainTitle
                        }}商城
                    </p>
                    <p class="l">当前等级：{{ userInfo.current_level_name }}
                        <img src="../../../assets/img/approve.png" @mouseenter="updateXY(thing.title, $event)" @mouseleave="updataFullFlag" class="img-appro" v-if="userInfo.authentication==2"/>
                    </p>
                    <p class="l integral" @click="integral">
                        我的积分：<span>{{ userInfo.current_integral }}</span>
                        再积累<span>{{ userInfo.next_integral }}</span
                        >积分 即可升级至 {{ userInfo.next_level_name }}
                    </p>
                </div>
                <p class="l my-address" @click="toMyAddress">我的收货地址</p>
                <div class="r xinxi">
                    <p class="l mine" @click="hits" :class="{ col: iscol }">
                        我的优惠信息<i class="el-icon-arrow-down"></i>
                    </p>
                    <span class="show" v-show="onoff">
                        <p @click="GoTo">店铺优惠券：<span>1</span></p>
                        <p>平台积分：<span>200</span></p>
                    </span>
                </div>
            </div>
            <div class="shoucang">
                <div class="thead">
                    <span
                        class="l"
                        @click="open(0)"
                        :class="{ bor: isbor == 0 }"
                        >商品收藏</span
                    >
                    <span
                        class="l"
                        @click="open(1)"
                        :class="{ bor: isbor == 1 }"
                        >店铺收藏</span
                    >
                    <router-link to="/collect" class="r" style="color:#666;"
                        >更多</router-link
                    >
                </div>
                <div class="bottom">
                    <ul class="clearfix" v-if="status">
                        <li
                            class="l"
                            v-for="(item, index) in goodsList"
                            :key="index"
                            @click="detail(item.goods_id)"
                            v-if="item.pic_url"
                        >
                            <img v-lazy="URL + item.pic_url"/>
                            <p class="text1-hidden" @mouseenter="updateXY(item.title, $event)" @mouseleave="updataFullFlag" v-if="item.title">{{ item.title }}</p>
                            <p v-if="item.price_member">￥{{ item.price_member }}</p>
                        </li>
                    </ul>
                    <ul class="clearfix" v-else>
                        <li
                            class="l"
                            v-for="(item, index) in shopList"
                            :key="index"
                            @click="storeDetail(item.id)"
                        >
                            <img v-lazy="URL + item.store_logo" />
                            <p class="text1-hidden" @mouseenter="updateXY(item.shop_name, $event)" @mouseleave="updataFullFlag">{{ item.shop_name }}</p>
                        </li>
                    </ul>
                </div>
            </div>
            <div class="like">
                <p class="top">
                    根据浏览，猜您喜欢<span
                        class="r another"
                        @click="myCollectionlikes"
                        >换一批<img src="../../../assets/img/huanyipi.jpg"
                    /></span>
                </p>
                <ul>
                    <li
                        class="l"
                        :key="i"
                        v-for="(item, i) in likeList"
                        @click="detail(item.gid)"
                    >
                        <img :src="URL + item.pic_url" />
                        <p class="text1-hidden" @mouseenter="updateXY(item.title, $event)" @mouseleave="updataFullFlag">{{ item.title }}</p>
                        <p>
                            {{ item.price_member }}
                            <span
                                style="margin-left:2px;color:#656565;font-size:11px"
                                >已售{{ item.sales_sum }}件</span
                            >
                        </p>
                    </li>
                </ul>
            </div>
        </div>
        <div class="right r">
            <div class="same l">
                <div class="me l">
                    <p>浏览记录</p>
                </div>
                <ul class="l">
                    <li
                        class="l"
                        v-for="(item, index) in browseList"
                        :key="index"
                        @click="detail(item.gid)"
                    >
                        <img v-lazy="URL + item.pic_url" />
                        <p>￥{{ item.price_member }}</p>
                        <p
                            style="color:#656565;position:relative;top: -12px;font-size:11px"
                        >
                            已售{{ item.sales_sum }}件
                        </p>
                    </li>
                </ul>
                <div class="base l" @click="ToView">
                    查看全部<span>({{ browseList.length }})</span>
                </div>
            </div>
        </div>
        <div
            class="full-display-title"
            v-if="fullTitieFlag"
            :style="displayTitleStyle"
        >
            {{ fullTitle }}
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            user_name: "",
            fullTitieFlag: false,
            displayTitleStyle: {
                top: "",
                bottom: ""
            },
            fullTitle: "",
            thing:{
                title:"已实名认证"
            },
            status: true,
            shopList: [],
            goodsList: [],
            likeList: [],
            browseList: [],
            iscol: "",
            isbor: "",
            onoff: "",
            userInfo: {},
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
        // let sss = localStorage.getItem("loginuserdata");
        // alert(sss)
        if(localStorage.getItem("loginuserdata") == 'true') {
            this.getGoodsCollection(); //商品收藏
            this.myCollectionlikes(); //猜你喜欢
            this.browseRecords(); //浏览记录
            this.getInfo(); //个人资料
            this.user_name = sessionStorage.getItem("userName");
            // this.user_name = localStorage.getItem("userNamee");
              let title = sessionStorage.getItem('titleKey') + '-' +sessionStorage.getItem('updateDescription');
            this.showScroll.scrollTitle(title);
        } else {
            this.$router.push('/passwordLogin')
        }
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
        updateXY(title, e) {
            this.fullTitle = title;
            this.displayTitleStyle.top = e.pageY + 10 + "px";
            this.displayTitleStyle.left = e.pageX + 20 + "px";
            this.fullTitieFlag = true;
        },
        updataFullFlag() {
            this.fullTitieFlag = false;
        },
        // 积分链接
        integral() {
            this.$router.push({
                path: "/myIntegral"
            });
        },
        // 查看全部链接
        ToView() {
            this.$router.push({
                path: "/ecentBrowse"
            });
        },
        //店铺优惠链接
        GoTo() {
            this.$router.push({
                path: "/myCoupons"
            });
        },
        storeDetail(id) {
            this.$router.push(`/storehome/${id}`);
        },
        detail(id) {
            window.open(window.location.origin + "/shopsn_product?id=" + id);
        },
        getInfo() {
            this.HTTP(this.$httpConfig.userInfo, {}, "post").then(res => {
                this.userInfo = res.data.data;
                sessionStorage.setItem("userInfoKey", JSON.stringify(res.data.data));
            });
        },
        toMyAddress() {
            this.$router.push("receive");
        },
        getGoodsCollection() {
            this.HTTP(this.$httpConfig.goodsCollection, {}, "post", false).then(
                res => {
                    this.goodsList = res.data.data.data;
                }
            );
        },
        getShopCollection() {
            this.HTTP(this.$httpConfig.shopCollection, {}, "post", false).then(
                res => {
                    this.shopList = res.data.data;
                }
            );
        },
        browseRecords() {
            this.HTTP(this.$httpConfig.myCollectionRecords, {}, "post").then(
                res => {
                    this.browseList = res.data.data;
                }
            );
        },
        //猜你喜欢
        myCollectionlikes() {
            this.HTTP(this.$httpConfig.myCollectionlikes, {}, "post").then(
                res => {
                    this.likeList = res.data.data;
                }
            );
        },
        hits() {
            this.onoff = !this.onoff;
            this.iscol = !this.iscol;
        },
        open(index) {
            if (index == 0) {
                this.getGoodsCollection();
                this.status = true;
            } else {
                this.getShopCollection();
                this.status = false;
            }
            this.isbor = index;
        }
    }
};
</script>

<style lang="less" scoped>
.full-display-title {
    position: absolute;
    z-index: 222;
    background: #ffffff;
    border: 1px solid #767676;
    padding: 4px;
    color: #575757;
}
.integral {
    cursor: pointer;
}
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

.greet {
    float: left;
    .middle {
        overflow: hidden;
        width: 726px;
        margin-left: 10px;
        margin-top: 16px;
        .up {
            height: 100px;
            background: #fff;
            width: 100%;
            div.l {
                width: 434px;
                img {
                    margin: 10px 14px 0 12px;
                    width: 50px;
                    height: 50px;
                }
                p {
                    width: 320px;
                    font-size: 12px;
                    line-height: 22px;
                    color: #999;
                    margin-left: 5px;
                    span {
                        color: #d02629;
                    }
                }
                p:nth-of-type(1) {
                    margin-top: 18px;
                    color: #333;
                }
                .img-appro{
                    width: 28px;
                    height: 28px;
                    margin: 0 0 0 10px;
                }
            }
            .my-address {
                font-size: 12px;
                color: #666;
                margin-top: 48px;
                cursor: pointer;
            }
            .xinxi {
                font-size: 12px;
                color: #666;
                margin: 48px 29px 0 0;
                position: relative;
                cursor: pointer;
                i {
                    margin-left: 5px;
                    z-index: 999;
                    position: relative;
                }
                .mine {
                    position: relative;
                    z-index: 999;
                }
                .show {
                    width: 159px;
                    height: 141px;
                    border: 1px solid #e7e6e6;
                    position: absolute;
                    top: -14px;
                    left: -29px;
                    background: #fff;
                    z-index: 33;
                    p:nth-of-type(1) {
                        margin-top: 45px;
                    }
                    p {
                        margin-left: 35px;
                        font-size: 13px;
                        color: #666;
                    }
                    span {
                        color: #d02629;
                    }
                    p:nth-of-type(2) {
                        margin-top: 16px;
                    }
                }
                .col {
                    color: #d02629;
                }
            }
        }
        .shoucang {
            background: #fff;
            margin-top: 11px;
            height: 408px;
            .thead {
                height: 43px;
                line-height: 42px;
                border-bottom: 1px solid #e8e8e8;
                span.l {
                    width: 84px;
                    text-align: center;
                    font-size: 14px;
                    color: #666;
                    cursor: pointer;
                }
                span.r {
                    font-size: 9px;
                    color: #a8a8a8;
                    margin-right: 12px;
                }
                .bor {
                    border-bottom: 1px solid #d02629;
                    color: #d02629 !important;
                }
            }
            .bottom {
                background: #fff;
                ul {
                    margin: 5px 0 0 14px;
                    li {
                        width: 100px;
                        height: 143px;
                        margin-top: 13px;
                        text-align: center;
                        margin-right: 20px;
                        img {
                            width: 100px;
                            height: 100px;
                        }
                        img:hover {
                            -webkit-transform: scale(1.1); /*1.1放大值*/
                            -moz-transform: scale(1.1);
                            -o-transform: scale(1.1);
                            -ms-transform: scale(1.1);
                        }
                        p:nth-of-type(1) {
                            font-size: 12px;
                            color: #666;
                            line-height: 20px;
                            margin-top: 5px;
                            &:hover {
                                color: red;
                            }
                        }
                        p:nth-of-type(2) {
                            font-size: 12px;
                            color: #e31939;
                            line-height: 18px;
                        }
                    }
                    li:nth-child(6n) {
                        margin-right: 0;
                    }
                }
            }
        }
        .like {
            height: 279px;
            background: #fff;
            margin-top: 13px;
            overflow: hidden;
            .top {
                margin: 17px 0 22px 19px;
                font-size: 14px;
                color: #484848;
                span {
                    font-size: 13px;
                    color: #a0a0a0;
                    img {
                        margin: -1px 15px 0 14px;
                    }
                }
                .another {
                    cursor: pointer;
                    img {
                        width: 20px;
                        height: 20px;
                    }
                }
            }
            ul {
                overflow: hidden;
                margin-left: 20px;
                li {
                    width: 110px;
                    height: 195px;
                    margin-right: 9px;
                    img {
                        width: 100%;
                        height: 110px;
                    }
                    img:hover {
                        -webkit-transform: scale(1.1); /*1.1放大值*/
                        -moz-transform: scale(1.1);
                        -o-transform: scale(1.1);
                        -ms-transform: scale(1.1);
                    }
                    p:nth-of-type(1) {
                        font-size: 12px;
                        color: #666;
                        line-height: 20px;
                        margin-top: 5px;
                        overflow: hidden;
                        white-space: nowrap;
                        text-overflow: ellipsis;
                        &:hover {
                            color: red;
                        }
                    }
                    p:nth-of-type(2) {
                        font-size: 10px;
                        color: #e30707;
                        line-height: 20px;
                    }
                }
                li:last-child {
                    margin-right: 0;
                }
            }
        }
    }
    .right {
        width: 235px;
        overflow: hidden;
        margin-top: 16px;
        .same {
            margin-bottom: 10px;
            background: #fff;
            margin-left: 15px;
            .me {
                height: 42px;
                border-bottom: 1px solid #f5f8fa;
                width: 235px;
                p {
                    line-height: 18px;
                    width: 90px;
                    border-left: 2px solid #d02629;
                    text-align: center;
                    margin-top: 14px;
                    margin-left: 15px;
                    float: left;
                }
            }
            ul {
                margin-top: 10px;
                margin-left: 8px;
                margin-bottom: 10px;
                li {
                    width: 80px;
                    height: 134px;
                    margin: 0 15px;
                    img {
                        width: 80px;
                        height: 80px;
                    }
                    img:hover {
                        -webkit-transform: scale(1.1); /*1.1放大值*/
                        -moz-transform: scale(1.1);
                        -o-transform: scale(1.1);
                        -ms-transform: scale(1.1);
                    }
                    p {
                        line-height: 30px;
                        text-align: center;
                        font-size: 10px;
                        color: #666;
                    }
                }
            }
            .base {
                cursor: pointer;
                height: 41px;
                border-top: 1px solid #f5f8fa;
                line-height: 41px;
                text-align: center;
                width: 100%;
                span {
                    color: #d02629;
                }
            }
        }
    }
}
</style>
