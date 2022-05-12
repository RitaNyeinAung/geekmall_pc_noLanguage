<template>
    <footer>
        <div class="top">
            <div class="center">
                <div class="erweima l">
                    <img :src="URL + websiteInfo.init_qr_code" />
                    <p>贡品商城官方微信服务号扫一扫，享更多优惠</p>
                </div>
                <div class="phone l">
                    <span class="call">{{ websiteInfo.intnet_phone }}</span>
                    <br />
                    <span class="workday">工作日(9:00-18:00)</span>
                </div>
                <div class="topul l">
                    <div class="l" v-for="(item, k) in article_category" :key="k">
                        <span @click="toLink(item.id, item.name)" class="c">
                            {{
                            item.name
                            }}
                        </span>
                        <ul>
                            <li
                                @click="hit(data.id,data.name,item.name)"
                                :key="index"
                                v-for="(data, index) in article"
                            >
                                <span v-if="data.article_category_id == item.id">
                                    {{ data.name }}
                                </span>
                            </li>
                        </ul>
                    </div>
                </div>
            </div>
        </div>
        <div class="middle">
            <div class="center">
                <img src="../assets/img/pinpai.jpg" />
            </div>
        </div>
        <div class="bottom">
            <div class="center">
                <p class="pOne l">{{ websiteInfo.intnet_licence }}</p>
                <p class="pOne l">{{ websiteInfo.intnet_copyright }}.
                    <a href="http://www.geekmall.plus" >{{siteLink}}</a>
                    <!-- <span class="sitelinkcss" @click="websiteCopyRight"> {{siteLink}} </span>   -->
                    <a target="_black" href="https://beian.miit.gov.cn/">{{websiteInfo.record_number}}</a>
                </p>
                <!-- <img src="../assets/img/biaozhi.jpg"/> -->
                 <p class="pOne l">
                    有任何问题请联系我们在线客服 电话：{{
                    websiteInfo.intnet_phone
                    }}
                </p>
                <!-- <img src="../assets/img/biaozhi.jpg"/> -->
                <ul class="partner">
                    <li>
                        <img src="../assets/img/unionpay.jpg" />
                        <img src="../assets/img/alipay.jpg" />
                        <img src="../assets/img/wx.jpg" />
                        <img src="../assets/img/Public-information-security.jpg" />
                        <img src="../assets/img/net-supervisor.jpg" />
                        <img src="../assets/img/trusted-website.jpg" />
                        <img src="../assets/img/Record-information.jpg" />
                    </li>
                </ul>
            </div>
        </div>
    </footer>
</template>
<script>
export default {
    data() {
        return {
            article_category: [],
            article: [],
            websiteInfo: {},
            siteLink: 'www.geekmall.plus'
        };
    },
    created() {
        this.getData();
        this.getFootData();
    },
    methods: {
        websiteCopyRight() {
            location.href = 'http://www.geekmall.plus';
        },
        //标题跳转
        toLink(id, name) {
            this.$emit('handleTitleClick',id,name)
            this.$router.push({
                name: "guide",
                params: {
                    type: "article",
                    name: name,
                    category_id: id
                }
            });
        },
        //底部合作伙伴图
        // getpartner() {
        //     this.HTTP(this.$httpConfig.getCooprativeList, {}, "post")
        //         .then(res => {
        //             this.partner = res.data.data;
        //         })
        //         .catch(e => {
        //             console.log(e);
        //         });
        // },
        getData() {
            this.HTTP(this.$httpConfig.commonFloor, {}, "post").then(res => {
                this.article = res.data.data.article;
                this.article_category = res.data.data.atricle_category;
            });
        },
        getFootData() {
            this.HTTP(this.$httpConfig.aboutEtcetera, {}, "post").then(res => {
                this.websiteInfo = res.data.data;
                sessionStorage.setItem("webKey", JSON.stringify(res.data.data));
                sessionStorage.setItem(
                    "recordKey",
                    res.data.data.record_number
                );
                sessionStorage.setItem("phoneKey", res.data.data.intnet_phone);
                sessionStorage.setItem(
                    "licenceKey",
                    res.data.data.intnet_licence
                );
                sessionStorage.setItem(
                    "copyrightKey",
                    res.data.data.intnet_copyright
                );
            });
        },
        hit(id,name,title) {
           this.$emit('handleDetailClick',id)
            this.$router.push({
                name: "guide",
                params: {
                    type: "articleItem",
                    title:title,
                    name:name,
                    id: id
                }
            });
        }
    }
};
</script>

<style lang="less" scoped>
.sitelinkcss {
    cursor: pointer;
}
.c {
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
footer {
    height: 564px;
    background: #f1f1f1;
    .top {
        height: 231px;
        border-bottom: 1px solid #e8e8e8;
        .erweima {
            width: 130px;
            img {
                margin: 46px 0 0 13px;
                width: 102px;
                height: 102px;
            }
            p {
                font-size: 12px;
                color: #b4b4b4;
                width: 100%;
                text-align: center;
                margin-top: 15px;
            }
        }
        .phone {
            margin-top: 83px;
            .call {
                color: #e44a56;
                font-size: 14px;
            }
            .workday {
                color: #b0b0b0;
                font-size: 10px;
            }
        }
        .topul {
            margin-top: 58px;
            margin-left: 102px;
            div {
                margin-right: 92px;
                span {
                    font-size: 15px;
                    color: #494949;
                }
                ul {
                    margin-top: 8px;
                    li span{
                        font-size: 12px;
                        color: #959595;
                        line-height: 24px;
                        cursor: pointer;
                        &:hover {
                            color: red;
                        }
                        width: 75px;
                        overflow: hidden;
                        text-overflow: ellipsis;
                        white-space: nowrap;
                    }
                }
            }
        }
    }
    .middle {
        height: 96px;
        border-bottom: 1px solid #e8e8e8;
        img {
            margin-top: 26px;
        }
    }
    .bottom {
        height: 236px;
        text-align: center;
        // ul{
        // 	margin-top: 30px;
        // 	margin-left: 34%;
        // 	text-align: center;
        // 	float: left;
        // 	li{
        // 		width: 75px;
        // 		float: left;
        // 		text-align: center;
        // 		border-left: 1px solid #989898;
        // 		cursor:pointer;
        // 		a{font-size: 12px;color: #a6a6a6;}
        // 		&:hover a{color:red;}
        // 	}
        // 	li:first-child{border: 0;}
        // }
        .pOne {
            width: 100%;
            text-align: center;
            color: #a6a6a6;
            font-size: 12px;
            margin-top: 10px;
        }
        .partner {
            padding-top: 33px;
            min-width: 1088px;
            min-height: 37px;
            display: flex;
            flex-wrap: nowrap;
            justify-content: center;
            flex-direction: row;
            .li {
                border: 0;
            }
        }
        // img{margin:33px 0 0 36px;}
    }
}
</style>
