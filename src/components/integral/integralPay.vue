<template>
    <div class="pay">
        <div class="center">
            <div class="header">
                <router-link to="/home">
                    <img class="l" :src="URL + logoPhoto" />
                </router-link>
                <p class="l">我的收银台</p>
            </div>
            <div class="middle">
                <p>此商品需要{{this.intergral}}积分， 当前可用{{currentIntergral}}积分</p>
                <p>
                    <span class="r">
                        应付金额
                        <span class="hong">{{$route.query.total_price|keep2Num}}</span>元
                    </span>
                </p>
                <p>请您在提交订单后24小时内完成支付。</p>
            </div>
            <div class="bottom">
                <p class="l">
                    账户当前余额
                    <span>{{balance|keep2Num}}</span>元
                </p>
                <ul class="fangshi l">
                    <li class="l" @click="change(0)" :class="{line:isline==0}">平台支付</li>
                    <li class="l" @click="change(1)" :class="{line:isline==1}">网银支付</li>
                </ul>
                <ul class="method l" v-show="isline==0">
                    <li
                        @click="toPayment(item.id)"
                        v-for="(item,index) in payType"
                        :key="index"
                        class="l"
                        v-if="item.id != 3"
                    >
                        <img class="l" :src="URL + item.logo" />
                        <span class="l">{{item.type_name}}</span>
                    </li>
                </ul>
                <ul class="method l" v-show="isline==1">
                    <li
                        @click="toPayment(item.id)"
                        v-for="(item,index) in payType"
                        :key="index"
                        class="l"
                        v-if="item.id == 3"
                    >
                        <img class="l" :src="URL + item.logo" />
                        <span class="l">{{item.type_name}}</span>
                    </li>
                </ul>
            </div>
            <div class="popup_div" v-show="popupVisible">
                <!-- <img class="cross-icon" @click="cancelArea" src="../../assets/img/clone-icon.png" />     -->
                <div class="popup_box">
                    <div class="password_div">
                    <p class="password_text">支付密码：</p>
                    <!-- <input class="password_input" type="text" v-model="balanceID" placeholder="请输入支付密码" v-if="checked"/>
                    <input class="password_input" type="password" v-model="balanceID" placeholder="请输入支付密码" v-else/>
                    <img class="eye_open"  @click="open" v-show="!eye" src="../../assets/img/openeye.png"/>
                    <img class="eye_close" @click="close" v-show="eye" src="../../assets/img/closeeye.png"/> -->
                    <v-otp-input
                        ref="otpInput"
                        input-classes="otp-input"
                        separator="-"
                        :num-inputs="6"
                        :should-auto-focus="true"
                        :is-input-num="true"
                        input-type="password"
                        @on-change="handleOnChange"
                        @on-complete="handleOnComplete"
                    />
                    </div>
                    <p class="time_message">{{this.timeMessage}}</p>
                    <button
                        class="next_button"
                        @click="confirmPayBtn">确认支付</button>
                </div>
            </div>
        </div>
        <foot-com></foot-com>
    </div>
</template>

<script>
import axios from "axios";
import config from "../../httpConfig"
export default {
    data() {
        return {
            isline: "",
            pay_id:0,
            balance: 0,
            payType: [],
            currentIntergral: 0,
            intergral: ~~this.$route.query.intergral,
            payId: "",
            payMethod: {
                1: "wechatPay",
                2: "aliPay",
                3: "ylPay",
                4: "confirmPay"
            },
            timeMessage: '',
			popupVisible: false,
            logoPhoto:'',
            payData: '',
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
        this.getPayResult();
        this.getIntegral();
        this.getBalance();
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
            confirmPayBtn() {
				this.HTTP(
						this.$httpConfig.balancePassword,
						{
							balance_password: this.balanceID
						},
						"post"
					)
					.then(res => {
						// this.$message(res.data.message);
						this.timeMessage = res.data.message;
						if(res.data.status == 1) {
							this.passwordError();
						}
						if(res.data.data == 10010) {
							this.popupVisible = false;
						}
					})
					.catch(err => {
						// this.$message(err.data.message);
						this.timeMessage = err.data.message;
					});
			},
			confirmPay() {
				this.popupVisible = true;
			},
			handleOnComplete(value) {
			},
			handleOnChange(value) {
				this.balanceID = value;
			},
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
                        this.logoPhoto = res.data.data.logo_name;
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
        //获取剩余积分
        getIntegral() {
            this.HTTP(this.$httpConfig.getIntegral, {}, "post").then(res => {
                this.currentIntergral = res.data.data.current_integral;
            });
        },
        //获取账号余额
        getBalance() {
            this.HTTP(this.$httpConfig.getBalance, {}, "post")
                .then(res => {
                    this.balance = res.data.data;
                })
                .catch(() => {});
        },
        //获取支付类型
        getPayResult() {
            this.HTTP(this.$httpConfig.getPayResult, {}, "post").then(res => {
                this.payType = res.data.data;
            });
        },
        // //余额支付
        // confirmPay() {
        //     this.HTTP(
        //         this.$httpConfig.integralPay,
        //         {
        //             pay_type_id: this.payId
        //         },
        //         "post"
        //     )
        //         .then(res => {
        //             axios.post(res.data.data).then(res => {
        //                 if (res.data == "SUCCESS") {
        //                     this.$router.push({
        //                         path: "/wxSuccess",
        //                         query: {
        //                             link: "/integralOrder"
        //                         }
        //                     });
        //                 } else {
        //                     this.$alert("支付失败", "提示", {
        //                         confirmButtonText: "确定",
        //                         center: true,
        //                         lockScroll: false,
        //                         type: "warning"
        //                     });
        //                 }
        //             });
        //         })
        //         .catch(res => {
        //             this.$alert(res.data.message, "提示", {
        //                 confirmButtonText: "确定",
        //                 center: true,
        //                 lockScroll: false,
        //                 type: "warning"
        //             });
        //         });
        // },
        //支付宝支付
        aliPay() {
            this.HTTP(
                this.$httpConfig.integralPay,
                {
                    pay_type_id: this.payId
                },
                "post"
            )
                .then(res => {
                    const oDiv = document.createElement("div");
                    oDiv.innerHTML = res.data.data;
                    document.body.appendChild(oDiv);
                    document.forms.Alipaysubmit.submit();
                })
                .catch(res => {
                    this.$alert(res.data.message, "提示", {
                        confirmButtonText: "确定",
                        center: true,
                        lockScroll: false,
                        type: "warning"
                    });
                });
        },
        wechatPay() { //微信支付跳转
            if (this.$route.query.total_price) { //普通商品
                this.$router.push({
                    name: 'wxpay',
                    params: {
                        total_price: this.$route.query.total_price,
                        id: this.payId,
                        state: 'q'
                    }
                })
            }
        },
			toPayment(id) {
				let that = this;
				this.payId = id;
				eval('that.' + that.payMethod[this.payId] + '()');
			},
        //余额支付
        passwordError() {
            this.popupVisible = false;
			this.balanceID = null;
                this.HTTP(
                    this.$httpConfig.integralPay,
                    {
                        pay_type_id: this.payId
                    },
                    "post"
                )
                    .then(res => {
                        // axios.post(res.data.data).then(res => {
                        //     if (res.data == "SUCCESS") {
                        //         this.$router.push({
                        //             path: "/wxSuccess",
                        //             query: {
                        //                 link: "/myOrder"
                        //             }
                        //         });
                        //     } else {
                        //         this.$alert("支付失败", "提示", {
                        //             confirmButtonText: "确定",
                        //             center: true,
                        //             lockScroll: false,
                        //             type: "warning"
                        //         });
                        //     }
                        // });
                        this.payData = res.data.data.data;
                        var url_ = this.payData;
                        axios.post(url_).then(res => {
                            if (res.data.status == 1) {
                                this.$router.push({
                                    path: "/myOrder"
                                });
                            } else {
                                this.$alert("支付失败", "提示", {
                                    confirmButtonText: "确定",
                                    center: true,
                                    lockScroll: false,
                                    type: "warning"
                                });
                            }
                        });
                    })
                    .catch(res => {
                        this.$alert(res.data.message, "提示", {
                            confirmButtonText: "确定",
                            center: true,
                            lockScroll: false,
                            type: "warning"
                        });
                    });      
        },
        change(index) {
            this.isline = index;
        }
    }
};
</script>

<style>
.el-message-box--center {
    padding-bottom: 30px !important;
}

.el-message-box--center .el-message-box__header {
    padding-top: 30px !important;
}
</style>

<style lang="less" scoped>
.popup_div {
    // width: 400px;
    height: auto;
    background: #ffffff;
    // z-index: 9999;
    position: absolute;
    // border: 1px solid #f2f2f2;
    // overflow: scroll;
    // margin-top: -48px;
    // margin-left: 400px;
    // box-shadow: 5px 5px 10px 0px rgba(0, 0, 0, 0.3);
    // -moz-box-shadow: 5px 5px 10px 0px rgba(0, 0, 0, 0.3);
    // -webkit-box-shadow: 5px 5px 10px 0px rgba(0, 0, 0, 0.3);
    // -o-box-shadow: 5px 5px 10px 0px rgba(0, 0, 0, 0.3);
    // -ms-box-shadow: 5px 5px 10px 0px rgba(0, 0, 0, 0.3);
    .picker-toolbar {
        display: flex;
    }
    .popup_box{
        // padding: 60px 20px 40px 30px;
        padding: 60px 20px 40px 22px;
        .verify_div {
            padding-top: 10px;
            .verify_text {
                float: left;
                // padding: 7px 18px 0 0;
                padding: 7px 14px 0 0;
            }
            .verify_input {
                width: 200px;
                height: 38px;
                border: 1px solid #ccc;
                border-radius: 3px;
                padding: 10px;
            }
            .verify_span {
				cursor: pointer;
				width: 136px;
				height: 34px;
				border: 1px solid #d9d9d9;
				background: #f6f6f6;
				display: inline-block;
				text-align: center;
				line-height: 34px;
				margin: -34px 0 0 393px;
                float: right;
			}
			.active {
				cursor: pointer;
				width: 136px;
				height: 34px;
				border: 1px solid #d9d9d9;
				background: #f6f6f6;
				display: inline-block;
				text-align: center;
				line-height: 34px;
				margin: -34px 0 0 393px;
                float: right;
			}
        }
        .password_div {
            .password_text {
                float: left;
                // padding: 7px 18px 0 0;
                padding: 7px 0 0 0;
            }
            .password_input {
                width: 200px;
                height: 38px;
                border: 1px solid #ccc;
                border-radius: 3px;
                padding: 10px;
            }
            .eye_open{
                width: 20px;
                margin-left: -30px;
            }
            .eye_close{
                width: 16px;
                margin-left: -30px;
            }
        }
        .time_message {
            padding: 15px 0 0 74px;
            color: red;
        }
        .next_button {
			cursor: pointer;
			border-radius: 3px;
			width: 110px;
			height: 38px;
			background: #afd129;
			text-align: center;
			line-height: 38px;
			color: #fff;
			font-size: 12px;
            // margin: 30px 0 0 87px;
            margin: 15px 0 0 73px;
		}
    }
    .cross-icon {
        width: 18px;
        height: 18px;
        float: right;
        margin: 10px;
    }
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
    background: #fff;
}

.hong {
    color: #d32026;
    font-size: 17px;
    margin: 0 11px;
}
.pay {
    background: #f6f5f5;
    height: 737px;
    .header {
        height: 109px;
        border-bottom: 1px solid #f6f5f5;
        img {
            margin: 31px 0 0 20px;
            width: 184px;
			height: 53px;
        }
        p {
            line-height: 30px;
            border-left: 1px solid #cdcfd0;
            font-size: 18px;
            color: #555;
            margin: 45px 0 0 20px;
            padding-left: 17px;
        }
    }
    .middle {
        height: 115px;
        margin-top: 41px;
        border-bottom: 1px solid #f6f5f5;
        p {
            float: left;
            font-size: 12px;
            margin-left: 21px;
            width: 80%;
        }
        p:nth-of-type(1) {
            color: #333;
            font-weight: 600;
            .dindan {
                font-weight: 500;
                margin-left: 19px;
            }
            span.r {
                font-weight: 500;
                margin-right: 42px;
            }
        }
    }
    .bottom {
        margin: 28px 0 0 22px;
        overflow: hidden;
        p {
            font-size: 12px;
            input {
                float: left;
                margin: 2px 14px 0 0;
            }
            span {
                color: #d32026;
            }
        }
        .fangshi {
            margin-top: 27px;
            height: 36px;
            border-bottom: 1px solid #f6f5f5;
            width: 100%;
            li {
                width: 70px;
                height: 37px;
                line-height: 34px;
                text-align: center;
                font-size: 15px;
                color: #555;
                margin-right: 22px;
                cursor: pointer;
            }
            .line {
                border-bottom: 2px solid #d02629;
                color: #d02629;
            }
        }
        .method {
            height: 99px;
            border-bottom: 1px solid #f6f5f5;
            width: 100%;
            margin-top: 28px;
            li {
                cursor: pointer;
                line-height: 40px;
                width: 100px;
                height: 40px;
                margin-right: 15px;
                img {
                    width: 26px;
                    height: 26px;
                    margin: 7px 5px 7px 0;
                }
            }
            li:hover {
                color: red;
            }
        }
    }
    .back {
        overflow: hidden;
        p {
            width: 170px;
            height: 42px;
            color: #fff;
            font-size: 18px;
            background: #ff5100;
            text-align: center;
            line-height: 42px;
            margin-top: 49px;
            margin-left: 20px;
            cursor: pointer;
        }
        a {
            font-size: 14px;
            margin: 65px 0 0 14px;
            text-decoration: underline;
            float: left;
        }
    }
}
</style>
