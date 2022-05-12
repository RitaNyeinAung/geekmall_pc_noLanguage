<template>
    <div id="page">
        <div id="topLogo">
            <div class="center">
                <ul class="l">
                    <li class="l logo">
                        <!-- <img :src="URL + logoPhoto" /> -->
                        <img :src="this.$httpConfig.readLogo" />
                    </li>
                    <li class="l category">
                        <img src="../../assets/img/register_text.png" />
                    </li>
                </ul>
                <div class="r already">
                    <p class="l have">已有账号</p>
                    <p class="l please">
                        <router-link to="passwordLogin" class="inPage"
                            >请登录</router-link
                        >
                    </p>
                </div>
            </div>
        </div>
        <div id="inform">
            <div class="center">
                <div class="freeBox"></div>
                <div class="resist">
                    <div class="l emptyResist">
                        <ul>
                            <li
                                class="l"
                                @click="loginEnter(0)"
                                :class="{ try: istry == 0 }"
                            >
                                账号注册
                            </li>
                            <!-- <li class="l" @click="loginEnter(1)" :class="{try:istry==1}">手机注册</li> -->
                        </ul>
                        <div class="lab" v-show="onoff == 0">
                            <div class="check-tips">
                                <el-alert
                                    v-if="checkText"
                                    :title="checkText"
                                    :type="checkType"
                                    show-icon
                                    :closable="false"
                                ></el-alert>
                            </div>
                            <p class="tabname">
                                用户名：<input
                                    type="text"
                                    placeholder="请输入最多20个 中 英 或 数字 组成的 用户名"
                                    v-model.trim="username"
                                />
                            </p>
                            <p class="tabname" v-if="$store.state.loginMethod == 0">
                                手机号：<input
                                    type="text"
                                    placeholder="请输入手机号"
                                    v-model="mobile"
                                /><span
                                    class="abtain"
                                    :class="{ active: isActive }"
                                    @click="abtain"
                                    >{{ btnText }}</span
                                >
                            </p>
                            <p class="tabname" v-if="$store.state.loginMethod == 1" style="text-indent: 3em;">
                                邮箱：<input
                                    type="text"
                                    placeholder="请输入常用邮箱"
                                    v-model="mailAcc"
                                /><span
                                    class="abtain"
                                    :class="{ active: isActive }"
                                    @click="abtain"
                                    >{{ btnText }}</span
                                >
                            </p>
                            <p class="checks">
                                验证码：<input
                                    type="text"
                                    placeholder="输入验证码"
                                    v-model="message"
                                />
                            </p>
                            <p class="tabs1" v-if="$store.state.loginMethod == 0">
                                邮箱：<input
                                    type="text"
                                    placeholder="请输入常用邮箱"
                                    v-model="email"
                                />
                            </p>
                            <p class="tabs1" v-if="$store.state.loginMethod == 1" style="text-indent: 1.9em;">
                                手机号：<input
                                    type="text"
                                    placeholder="请输入手机号"
                                    v-model="phone"
                                />
                            </p>
                            <p class="tab">
                                设置密码：<input
                                    type="password"
                                    placeholder="6-20位大小写英文字母 数字 或符号"
                                    v-model="password"
                                />
                            </p>
                            <p class="tab1">
                                确认密码：<input
                                    type="password"
                                    placeholder="请再次输入密码"
                                    v-model="re_password"
                                />
                            </p>
                            <p class="consign">
                                <input
                                    type="checkbox"
                                    v-model="choose"
                                />阅读并同意<a>《服务协议》</a>
                            </p>
                            <p class="button agree" @click="register">
                                立即注册
                            </p>
                        </div>
                        <!-- <div class="labs" v-show='onoff==1'>
								<div class="check-tips">
									<el-alert v-if="checkText1" :title="checkText1" :type="checkType1" show-icon :closable="false"></el-alert>
								</div>
								<p class="tabsAdd">手机号：<input type="text" placeholder="请输入手机号" maxlength="11" v-model="mobile"/></p>
								<p class="checks">验证码：<input type="text" placeholder="输入验证码" v-model="numbers"/><img src="../../assets/img/register_yanzhengma.png"/></p>
								<p class="correct">确保验证码输入正确，点击<a class="access" @click="send"><img src="../../assets/img/post.png"/></a>并将您手机短信所接收的"六位验证码"输入到下方短信验证，在提交下一步</p>
								<p class="checked">短信验证码：<input type="text" placeholder="输入六位验证码" v-model="ent"/></p>
								<p class="button agree" @click="ipEnter">立即注册</p>
							</div> -->
                    </div>
                    <div class="l emptyResists">
                        <div class="friend">
                            <p class="string">使用合作网站账号直接登录</p>
                            <p class="circle" v-if="$store.state.loginMethod == 0" style="margin-left: 25px">
                                <a class="l otherPeople"
                                    ><img src="../../assets/img/login_qq.png"
                                /></a>
                                <a class="l otherPeople"
                                    ><img src="../../assets/img/login_weibo.png"
                                /></a>
                                <a class="l otherPeople"
                                    ><img
                                        src="../../assets/img/login_weixin.png"
                                /></a>
                            </p>
                            <p class="circle" v-if="$store.state.loginMethod == 1" style="margin-left: 45px">
                                <!-- <a class="l otherPeople"
                                    ><img src="../../assets/img/Facebook_Logo.png"
                                /></a>
                                <a class="l otherPeople"
                                    ><img src="../../assets/img/google_logo.png"
                                /></a> -->
                                <facebook-login class="fb_button1"
                                    :appId="appId"
                                    loginLabel=""
                                    logoutLabel=""
                                    @login="getUserData"
                                    @logout="onLogout">
                                </facebook-login>
                                <GoogleLogin :params="params" :onSuccess="onSuccess" class="google_button1">
                                    <a class="l" style="width: 30px;">
                                        <i
                                            ><img
                                                style="width: 23px; height: 23px;"
                                                src="../../assets/img/google_logo.png"/>
                                        </i>
                                    </a>
                                </GoogleLogin>
                            </p>
                        </div>
                        <div class="haveOk">
                            <p class="afters">注册之后您可以</p>
                            <div class="full">
                                <dl>
                                    <dt class="l">
                                        <img
                                            src="../../assets/img/register_car.png"
                                        />
                                    </dt>
                                    <dd class="l">购买商品支付订单</dd>
                                </dl>
                                <dl>
                                    <dt class="l">
                                        <img
                                            src="../../assets/img/register_collect.png"
                                        />
                                    </dt>
                                    <dd class="l">收藏商品关注商铺</dd>
                                </dl>
                                <dl>
                                    <dt class="l">
                                        <img
                                            src="../../assets/img/register_safe.png"
                                        />
                                    </dt>
                                    <dd class="l">安全交易诚信无忧</dd>
                                </dl>
                                <dl>
                                    <dt class="l">
                                        <img
                                            src="../../assets/img/register_grade.png"
                                        />
                                    </dt>
                                    <dd class="l">积分获取优惠购物</dd>
                                </dl>
                                <dl>
                                    <dt class="l">
                                        <img
                                            src="../../assets/img/register_enjoy.png"
                                        />
                                    </dt>
                                    <dd class="l">会员等级享受特权</dd>
                                </dl>
                                <dl>
                                    <dt class="l">
                                        <img
                                            src="../../assets/img/register_pingjia.png"
                                        />
                                    </dt>
                                    <dd class="l">评价晒单站外分享</dd>
                                </dl>
                            </div>
                        </div>
                    </div>
                </div>
            </div>
            <modal :backdrop-closable="false" title="请绑定邮箱" :width="400" :is-show="showEmailBind" transition="fadeDown"
                @close="showEmailBind=false" :show-footer="false">
                <div class="show_bind">
                    <label>
                        <i class="l mob"
                            ><img
                                src="../../assets/img/login_person.png"
                        /></i>
                        <input
                            class="l mob"
                            type="text"
                            placeholder="请输入手机号"
                            v-model="fbData.name"
                            disabled
                        />
                    </label>
                    <label>
                        <i class="l"
                            ><img
                                src="../../assets/img/login_info.png"
                        /></i>
                        <input
                            class="l"
                            type="text"
                            placeholder="请输入常用邮箱"
                            v-model="fb_email"
                        />
                    </label>
                    <p
                        class="abtain"
                        :class="{ active: isActive }"
                        @click="emailAbtain"
                    >
                        {{ btnText }}
                    </p>
                    <label>
                        <i class="l"
                            ><img src="../../assets/img/login_lock.png"
                        /></i>
                        <input
                            class="l"
                            type="text"
                            placeholder="输入验证码"
                            v-model="fb_code"
                        />
                    </label>
                    <button
                        class="button loginIn"
                        @click="fbLoginFun"
                    >
                        登录
                    </button>
                </div>
            </modal>
        </div>
        <com-foot @handleItemInfo="handleItemInfo"> </com-foot>
    </div>
</template>

<script>
import Qs from "qs";
import ComFoot from "@/common/footerDetail.vue";
import GoogleLogin from 'vue-google-login';
import facebookLogin from 'facebook-login-vuejs';
export default {
    components: {
        ComFoot,
        GoogleLogin,
        facebookLogin
    },
    data() {
        return {
            appId: facebook_app_id,
            params: {
                client_id: google_client_id
            },
            showEmailBind: false,
            fbData: [],
            fb_email: "",
            fb_code: "",
            isActive: false,
            onoff: "",
            istry: "",
            username: "",
            mobile: "",
            mailAcc: "",
            message: "",
            password: "",
            re_password: "",
            email: "",
            phone: "",
            btnText: "获取验证码",
            numbers: "",
            ent: "",
            choose: true,
            checkText: "",
            checkText1: "",
            checkType: "error",
            checkType1: "error",
            logoPhoto:'',
            headParams: {
                title: sessionStorage.getItem('titleKey'),
                description: sessionStorage.getItem('updateDescription'),
                keywords: sessionStorage.getItem('contentKey'),
                link:sessionStorage.getItem('pcfavicon')         
            }
        };
    },

	created() {
        this.getFootData();
        this.getFavIcon(); 
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
    methods: {
        onSuccess(googleUser) {
            console.log("google login", googleUser.wc.access_token);
            sessionStorage.clear();
            sessionStorage.removeItem('userName')
            sessionStorage.removeItem('userHeaderImg')
            sessionStorage.removeItem('token')

            this.HTTP(
                this.$httpConfig.getLogin,
                {
                    access_token: googleUser.wc.access_token
                },
                "post"
            )
                .then(res => {
                    localStorage.setItem("tokenn", res.data.data.token);
                    localStorage.setItem("loginuserdata", 'true');
                    this.HTTP(this.$httpConfig.userInfo, {}, "post").then(
                        res => {
                            let site = sessionStorage.getItem("site");
                            if (site != undefined) {
                                setTimeout(() => {
                                    this.$router.push(site);
                                }, 1000);
                            } if(res.data.status == 1) {
                                sessionStorage.setItem('userName', res.data.data.user_name);
                                sessionStorage.setItem('userHeaderImg', res.data.data.user_header);

                                setTimeout(() => {
                                    this.$message({
                                        message: `${res.data.message}`
                                    });
                                    this.$router.push("/home");
                                }, 1000);
                            } else {
                                this.$message(`${res.data.message}`);
                            }
                        }
                    );
                })
                .catch(err => {
                    this.$message({
                        message: `${err.data.message}`
                    });
                });
        },
        getUserData(data) {
            console.log("facebook login data", JSON.stringify(data))
            sessionStorage.clear();
            sessionStorage.removeItem('userName')
            sessionStorage.removeItem('userHeaderImg')
            sessionStorage.removeItem('token')

            this.HTTP(
                this.$httpConfig.getFBLogin,
                {
                    access_token: data.response.authResponse.accessToken
                },
                "post"
            )
                .then(res => {
                    this.fbData = res.data.data;
                    if(res.data.data.binding == 1) {
                        this.showEmailBind = !this.showEmailBind;
                        this.$message({
                            message: "请绑定邮箱",
                            duration: 1500
                        })
                    } if(res.data.data.binding == 0) {
                        localStorage.setItem("tokenn", res.data.data.token);
                        localStorage.setItem("loginuserdata", 'true');
                        this.HTTP(this.$httpConfig.userInfo, {}, "post").then(
                            res => {
                                let site = sessionStorage.getItem("site");
                                if (site != undefined) {
                                    setTimeout(() => {
                                        this.$router.push(site);
                                    }, 1000);
                                } if(res.data.status == 1) {
                                    sessionStorage.setItem('userName', res.data.data.user_name);
                                    sessionStorage.setItem('userHeaderImg', res.data.data.user_header);

                                    setTimeout(() => {
                                        this.$message({
                                            message: `${res.data.message}`
                                        });
                                        this.$router.push("/home");
                                    }, 1000);
                                } else {
                                    this.$message(`${res.data.message}`);
                                }
                            }
                        );
                    }
                })
                .catch(err => {
                    this.$message({
                        message: `${err.data.message}`
                    });
                });
        },
        onLogout(data) {
            console.log("facebook logout data",  JSON.stringify(data))
        },
        emailAbtain() {
            if (this.isActive == true) return;
            if (
                !/^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/.test(
                    this.fb_email
                )
            ) {
                this.checkText = "请输入正确的邮箱地址";
                return false;
            }
            this.HTTP(
                this.$httpConfig.getSendMailbox,
                {
                    email: this.fb_email
                },
                "post"
            ).then(res => {
                if (res.data.status == 1) {
                    this.token = res.data.data.token;
                    var N = 60,
                        _this = this,
                        clear = null;
                    _this.isActive = true;
                    _this.btnText = "请" + N + "秒后重试";
                    _this.isActive = true;
                    clear = setInterval(function() {
                        _this.btnText = "请" + N-- + "秒后重试";
                        if (N < 0) {
                            clearInterval(clear);
                            _this.btnText = "获取验证码";
                            _this.isActive = false;
                        }
                    }, 1000);
                } else {
                    clearInterval(clear);
                    _this.btnText = "再次获取验证码";
                    _this.isActive = false;
                    this.$message(`${res.data.message}`);
                }
            });
        },
        fbLoginFun() {
            if (
                !/^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/.test(
                    this.fb_email
                )
            ) {
                this.checkText = "请输入正确的邮箱地址";
                return false;
            }
            if (this.fb_code.length != 7) {
                this.checkText = "验证码错误";
                return false;
            }
            this.HTTP(
                this.$httpConfig.getBindingEmail,
                {
                    user_name: this.fbData.name,
                    email: this.fb_email,
                    code: this.fb_code,
                    open_id: this.fbData.open_id
                },
                "post"
            )
                .then(res => {
                    if(res.data.status == 1) {
                        sessionStorage.setItem("token", res.data.data.token);
                        this.HTTP(this.$httpConfig.userInfo, {}, "post", true).then(
                            res => {
                                sessionStorage.clear();
                                localStorage.clear();
                                sessionStorage.setItem(
                                    "userName",
                                    res.data.data.user_name
                                );
                                sessionStorage.setItem(
                                    "userHeaderImg",
                                    res.data.data.user_header
                                );
                                setTimeout(() => {
                                    this.$message({
                                        message: `${res.data.message}`
                                    });
                                    this.$router.push("/home");
                                }, 1000);
                            }
                        );
                    } else {
                        this.$message({
                            message: `${res.data.message}`
                        });
                    }
                })
                .catch(err => {
                    this.$message({
                        message: `${err.data.message}`
                    });
                });
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
        handleItemInfo(data) {
            // let title =
            //     "注册" +
            //     "-" +
            //     data.intnet_title +
            //     "-" +
            //     data.init_key_word +
            //     " ";
             let title = sessionStorage.getItem('titleKey') + '-' +sessionStorage.getItem('updateDescription');
            this.showScroll.scrollTitle(title);

        },
        abtain() {
            this.checkType = "error";
            if (this.isActive == true) return;
            // if (!this.myanmarPhoneNumber.isValidMMPhoneNumber(this.mobile)) {
            if (this.$store.state.loginMethod == 0) {
                if (!/^1[345789]\d{9}$/.test(this.mobile)) {
                    this.checkText = "请填写正确的手机号";
                    return;
                } else {
                    this.checkText = "";
                }
            }
            if (this.$store.state.loginMethod == 1) {
                if (
                    !/^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/.test(
                        this.mailAcc
                    )
                ) {
                    this.checkText = "请输入正确的邮箱地址";
                    return false;
                }
            }
            // var N = 60,
            //     _this = this,
            //     clear = null;
            // _this.isActive = true;
            // _this.btnText = "请" + N + "秒后重试";
            // _this.isActive = true;
            // clear = setInterval(function() {
            //     _this.btnText = "请" + N-- + "秒后重试";
            //     if (N < 0) {
            //         clearInterval(clear);
            //         _this.btnText = "获取验证码";
            //         _this.isActive = false;
            //     }
            // }, 1000);
            if (this.$store.state.loginMethod == 0) {
                this.HTTP(
                    this.$httpConfig.sms,
                    {
                        mobile: this.mobile
                    },
                    "post"
                )
                    .then(res => {
                        if (res.data.status == 1) {
                            this.checkText = "";
                            var N = 60,
                                _this = this,
                                clear = null;
                            _this.isActive = true;
                            _this.btnText = "请" + N + "秒后重试";
                            _this.isActive = true;
                            clear = setInterval(function() {
                                _this.btnText = "请" + N-- + "秒后重试";
                                if (N < 0) {
                                    clearInterval(clear);
                                    _this.btnText = "获取验证码";
                                    _this.isActive = false;
                                }
                            }, 1000);
                        } else {
                            this.checkText = res.data.message;
                        }
                        // this.token = res.data.data.token;
                    })
                    .catch(res => {
                        this.checkText = res.data.message;
                        this.checkType = "warning";
                    });
            }
            if (this.$store.state.loginMethod == 1) {
                this.HTTP(
                    this.$httpConfig.getSendMailbox,
                    {
                        email: this.mailAcc
                    },
                    "post"
                )
                    .then(res => {
                        if (res.data.status == 1) {
                            this.checkText = "";
                            var N = 60,
                                _this = this,
                                clear = null;
                            _this.isActive = true;
                            _this.btnText = "请" + N + "秒后重试";
                            _this.isActive = true;
                            clear = setInterval(function() {
                                _this.btnText = "请" + N-- + "秒后重试";
                                if (N < 0) {
                                    clearInterval(clear);
                                    _this.btnText = "获取验证码";
                                    _this.isActive = false;
                                }
                            }, 1000);
                        } else {
                            this.checkText = res.data.message;
                        }
                        // this.token = res.data.data.token;
                    })
                    .catch(res => {
                        this.checkText = res.data.message;
                        this.checkType = "warning";
                    });
            }
        },
        loginEnter(index) {
            this.onoff = index;
            this.istry = index;
        },
        send() {
            // this.HTTP(this.$httpConfig,{},'post')
            // .then((res) =>{
            // })
        },
        register() {
            this.checkType = "error";
            // ^[a-zA-Z0-9\u4E00-\u9FA5_].{6,20}$
            // ^(?![\d]+$)(?![a-zA-Z]+$)(?![^\da-zA-Z]+$).{6,20}$
            if (
                !/^[a-zA-Z0-9\u4E00-\u9FA5_].{0,20}$/.test(
                    this.username
                )
            ) {
                this.checkText = "请输入最多20个 中 英 或 数字 组成的 用户名";
                return false;
            }
            // if (!this.myanmarPhoneNumber.isValidMMPhoneNumber(this.mobile)) {
            if (this.$store.state.loginMethod == 0) {
                if (!/^1[345789]\d{9}$/.test(this.mobile)) {
                    this.checkText = "请填写正确的手机号";
                    return;
                } else {
                    this.checkText = "";
                }
            }
            if (this.$store.state.loginMethod == 1) {
                if (
                    !/^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/.test(
                        this.mailAcc
                    )
                ) {
                    this.checkText = "请输入正确的邮箱地址";
                    return false;
                }
            }
            if (this.message.length != 7) {
                this.checkText = "验证码错误";
                return false;
            }
            if (this.password.toString().length < 6) {
                this.checkText = "请输入至少6位的密码";
                return false;
            }
            if (this.re_password != this.password) {
                this.checkText = "两次密码输入不一致";
                return false;
            }
            if (this.$store.state.loginMethod == 0) {
                // if (
                //     !/^([a-zA-Z0-9_-])+@([a-zA-Z0-9_-])+(.[a-zA-Z0-9_-])+/.test(
                //         this.email
                //     )
                if (
                    !/^(([^<>()[\]\\.,;:\s@"]+(\.[^<>()[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/.test(
                        this.email
                    )
                ) {
                    this.checkText = "请输入正确的邮箱地址";
                    return false;
                }
            }
            if (this.$store.state.loginMethod == 1) {
                if (!this.myanmarPhoneNumber.isValidMMPhoneNumber(this.phone)) {
                    this.checkText = "请填写正确的手机号";
                    return;
                } else {
                    this.checkText = "";
                }
            }
            if (this.choose == false) {
                this.checkText = "请同意协议";
                return false;
            }

            if (this.$store.state.loginMethod == 0) {
                var data = {
                    user_name: this.username,
                    email: this.email,
                    password: this.password,
                    repassword: this.re_password,
                    // token: this.token,
                    code: this.message,
                    mobile: this.mobile
                }
            }
            if (this.$store.state.loginMethod == 1) {
                var data = {
                    user_name: this.username,
                    email: this.mailAcc,
                    password: this.password,
                    repassword: this.re_password,
                    // token: this.token,
                    code: this.message,
                    mobile: this.phone
                }
            }

            // this.checkType = 'success';
            // this.checkText = '注册成功,请稍等...';
            // setTimeout(() => {
            // 	this.$router.push('/passwordLogin');
            // }, 3000)
            this.HTTP(
                this.$httpConfig.register, data ,
                "post"
            )
                .then(res => {
                    // if (res.data.message === '手机号验证码错误') {
                    // 	this.checkText1 = '验证码有误'
                    // } else if (res.data.message === '账号已存在') {
                    // 	this.checkText1 = '您已注册'
                    // } else if (res.data.message === '邮箱已存在') {
                    // 	this.checkText1 = '您已注册该邮箱'
                    // }
                    if (res.data.status === 1) {
                        this.checkType1 = "success";
                        this.checkText1 = "注册成功,请稍等...";
                        this.$router.push("/passwordLogin");
                    }
                    this.$message({
                        message: `${res.data.message}`
                    });
                })
                .catch(e => {
                    this.$message({
                        message: `${e.data.message}`
                    });
                });
        },
        // ipEnter() {
        //     if (this.mobile.length != 11) {
        //         this.checkText1 = "手机号码有误,请重新输入";
        //         return false;
        //     }
        //     if (this.numbers.length != 4) {
        //         this.checkText1 = "验证码错误";
        //         return false;
        //     }
        //     if (this.ent.length != 6) {
        //         this.checkText1 = "短信验证码错误";
        //         return false;
        //     }
        //     this.checkType1 = "success";
        //     this.checkText1 = "注册成功,请稍等...";
        //     setTimeout(() => {
        //         this.$router.push("/passwordLogin");
        //     }, 3000);
        //     this.HTTP(
        //         this.$httpConfig.register,
        //         {
        //             mobile: this.mobile,
        //             user_name: this.username
        //         },
        //         "post"
        //     ).then(res => {
        //         this.$router.push("/passwordLogin");
        //     });
        // }
    }
};
</script>

<style lang="less">
.fb_button1 {
    width: 30px !important;
    border: 1px solid #fff !important;
    margin-left: 0px !important;
    margin-top: -5px !important;
    .spinner {
        box-sizing: border-box;
        width: 30px;
        height: 90%;
        border-radius: 50%;
        border: 5px solid #f3f3f3;
        border-top-color: rgb(243, 243, 243);
        border-top-style: solid;
        border-top-width: 5px;
        border-top: 5px solid #3498db;
        -webkit-animation: spin-data-v-0af1f3c3 2s linear infinite;
        animation: spin-data-v-0af1f3c3 2s linear infinite;
        position: absolute;
        left: 5px;
        display: none !important;
    }
    .fb_button .container {
        border: 1px solid #fff !important;
        float: left;
        margin-left: 14px;
        width: 30px !important;
        overflow: hidden;
        height: 80px !important;
        line-height: 80px !important;
    }
    button {
        position: relative;
        padding: 0 !important;
        border: none;
        line-height: 34px;
        font-size: 16px;
        color: #FFF;
        min-width: 22px !important;
        // background-image: linear-gradient(#4C69BA, #3B55A0);
        // border-radius: 3px;
        // margin-top: 20px;
        background-image:url(../../assets/img/Facebook_Logo.png) !important;
        background-size:100% 100%;
        width:22px;
        height:22px;
        border-radius: 50% !important;
        margin-top: 5px;
        }
        img {
            border: 0;
            vertical-align: middle;
            display: none !important;
        }
}
.google_button1 {
    background: #fff;
    font-size: 16px;

}
.modal-background {
  bottom: 0;
  left: 0;
  position: absolute;
  right: 0;
  top: 0;
  background-color: hsla(0,0%,4%,.5) !important;
}
</style>

<style lang="less" scoped>
.center {
    width: 1000px;
    height: 100%;
    margin: 0 auto;
}

.l {
    float: left;
}

.r {
    float: right;
}

#topLogo {
    width: 100%;
    height: 100px;
    .center {
        height: 100px;
        ul {
            width: 460px;
            height: 100px;
            .logo {
                width: 184px;
                height: 53px;
                margin-top: 21px;
                border-right: 1px solid #ebebeb;
                img{
                    width: 184px;
                    height: 53px;
                }
            }
            .category {
                margin-top: 35px;
                margin-left: 15px;
            }
        }
        .already {
            width: 150px;
            height: 100px;
            .have {
                width: 70px;
                height: 100px;
                line-height: 100px;
                font-size: 15px;
                color: #333333;
                font-weight: 500;
                text-align: center;
            }
            .please {
                width: 70px;
                height: 100px;
                line-height: 100px;
                .inPage {
                    font-size: 15px;
                    color: #d02629;
                    font-weight: 500;
                }
            }
        }
    }
}

#inform {
    width: 100%;
    height: 705px;
    background: url(../../assets/img/register_bg.png) no-repeat center;
    .center {
        height: 705px;
        .freeBox {
            height: 66px;
            width: 100%;
        }
        .resist {
            width: 858px;
            // height: 570px;
            height: 600px;
            border: 10px solid #d0d0d0;
            background: #ffffff;
            margin-left: 60px;
            position: relative;
            top: -58px;
            .emptyResist {
                width: 588px;
                // height: 550px;
                height: 580px;
                margin-left: 29px;
                border-right: 1px solid #e6e6e6;
                ul {
                    width: 588px;
                    height: 62px;
                    li {
                        width: 293px;
                        height: 62px;
                        line-height: 62px;
                        text-align: center;
                        color: #999999;
                        font-size: 18px;
                        border-bottom: 1px solid #e6e6e6;
                    }
                    .try {
                        background: url(../../assets/img/download.png) no-repeat
                            0 60px;
                        color: #d02629;
                    }
                }
                .check-tips {
                    height: 40px;
                    padding: 6px 0;
                    .el-alert {
                        padding: 4px 16px;
                    }
                }
                .lab {
                    width: 396px;
                    height: 320px;
                    margin-left: 103px;
                    // margin-top: 40px;
                    .tab {
                        width: 396px;
                        height: 55px;
                        line-height: 54px;
                        text-indent: 1em;
                        color: #555555;
                        font-size: 14px;
                        border-left: 1px solid #e6e6e6;
                        border-right: 1px solid #e6e6e6;
                        border-top: 1px solid #e6e6e6;
                        input {
                            width: 300px;
                            height: 50px;
                            font-size: 14px;
                            text-indent: 0.5em;
                        }
                    }
                    .tab1 {
                        width: 396px;
                        height: 56px;
                        line-height: 54px;
                        text-indent: 1em;
                        color: #555555;
                        font-size: 14px;
                        border: 1px solid #e6e6e6;
                        margin-bottom: 20px;
                        input {
                            width: 300px;
                            height: 50px;
                            font-size: 14px;
                            text-indent: 0.5em;
                        }
                    }
                    .tabname {
                        width: 396px;
                        height: 55px;
                        line-height: 54px;
                        text-indent: 1.9em;
                        color: #555555;
                        font-size: 14px;
                        border-left: 1px solid #e6e6e6;
                        border-right: 1px solid #e6e6e6;
                        border-top: 1px solid #e6e6e6;
                        position: relative;
                        input {
                            width: 300px;
                            height: 50px;
                            font-size: 14px;
                            text-indent: 0.5em;
                        }
                        .abtain {
                            text-indent: initial;
                            width: 90px;
                            height: 24px;
                            line-height: 24px;
                            border-radius: 12px;
                            border: 1px solid #c4c4c4;
                            font-size: 10px;
                            color: #a9a9a9;
                            text-align: center;
                            position: absolute;
                            top: 16px;
                            right: 30px;
                            cursor: pointer;
                        }
                        .active {
                            background: #c4c4c4;
                            color: #fff;
                        }
                    }
                    .tabs1 {
                        width: 396px;
                        height: 55px;
                        line-height: 54px;
                        text-indent: 3em;
                        color: #555555;
                        font-size: 14px;
                        border-left: 1px solid #e6e6e6;
                        border-right: 1px solid #e6e6e6;
                        border-top: 1px solid #e6e6e6;
                        input {
                            width: 300px;
                            height: 50px;
                            font-size: 14px;
                        }
                    }
                    .checks {
                        width: 396px;
                        height: 54px;
                        color: #555555;
                        font-size: 14px;
                        line-height: 54px;
                        text-indent: 1.9em;
                        border-left: 1px solid #e6e6e6;
                        border-right: 1px solid #e6e6e6;
                        border-top: 1px solid #e6e6e6;
                        input {
                            width: 202px;
                            height: 45px;
                            font-size: 14px;
                            text-indent: 0.5em;
                        }
                    }
                    .consign {
                        width: 300px;
                        height: 26px;
                        line-height: 26px;
                        margin-top: 12px;
                        input {
                            margin-right: 3px;
                        }
                    }
                }
                // .labs{
                // 	width: 396px;
                // 	height: 290px;
                // 	margin-left: 103px;
                // 	.tabsAdd{
                // 		width: 396px;
                // 		height: 54px;
                // 		line-height: 54px;
                // 		text-indent: 1em;
                // 		color: #555555;
                // 		font-size: 14px;
                // 		border-left: 1px solid #e6e6e6;
                // 		border-right: 1px solid #e6e6e6;
                // 		border-top: 1px solid #e6e6e6;
                // 		/*border-bottom: 1px solid #e6e6e6;*/
                // 		input{
                // 			width: 300px;
                // 			height: 50px;
                // 			font-size: 14px;
                // 		}
                // 	}
                // 	.checks{
                // 		width: 396px;
                // 		height: 54px;
                // 		color: #555555;
                // 		font-size: 14px;
                // 		line-height: 54px;
                // 		text-indent: 1em;
                // 		border: 1px solid #e6e6e6;
                // 		input{
                // 			width: 202px;
                // 			height: 45px;
                // 			font-size: 14px;
                // 		}
                // 		img{
                // 			width: 122px;
                // 			height: 52px;
                // 			margin: 0;
                // 			padding: 0;
                // 		}
                // 	}
                // 	.correct{
                // 		width: 390px;
                // 		height: 83px;
                // 		margin-top: 15px;
                // 		color: #999999;
                // 		font-size: 14px;
                // 		.access{
                // 			width: 115px;
                // 			height: 32px;
                // 		}
                // 	}
                // 	.checked{
                // 		width: 400px;
                // 		height: 52px;
                // 		line-height: 52px;
                // 		text-indent: 1em;
                // 		border: 1px solid #e6e6e6;
                // 		input{
                // 			height: 45px;
                // 			font-size: 14px;
                // 		}
                // 	}
                // }
                .agree {
                    width: 398px;
                    height: 42px;
                    line-height: 42px;
                    text-align: center;
                    /*margin-left: 10px;*/
                    margin-top: 20px;
                    background: #d02629;
                    color: #ffffff;
                    font-size: 17px;
                    border: 0;
                }
            }
            .emptyResists {
                width: 200px;
                // height: 550px;
                height: 580px;
                margin-left: 20px;
                .friend {
                    width: 200px;
                    height: 82px;
                    margin-top: 40px;
                    border-bottom: 1px dashed #e8e8e8;
                    .string {
                        width: 200px;
                        height: 26px;
                        line-height: 26px;
                        color: #333333;
                        font-size: 14px;
                    }
                    .circle {
                        // width: 170px;
                        height: 44px;
                        margin-top: 10px;
                        // margin-left: 12px;
                        /*margin-bottom: ;*/
                        a {
                            margin-left: 10px;
                        }
                        img {
                            width: 22px;
                            height: 22px;
                        }
                    }
                }
                .haveOk {
                    width: 200px;
                    height: 300px;
                    .afters {
                        width: 100%;
                        height: 28px;
                        line-height: 28px;
                        font-size: 14px;
                        color: #333333;
                        margin-top: 20px;
                    }
                    .full {
                        width: 100%;
                        height: 127px;
                        dl {
                            width: 100%;
                            height: 38px;
                            dt {
                                width: 43px;
                                height: 38px;
                                img {
                                    margin: 6px;
                                }
                            }
                            dd {
                                height: 38px;
                                line-height: 38px;
                            }
                        }
                    }
                }
            }
        }
    }
}

#commonFooter {
    width: 100%;
    height: 90px;
    .center {
        height: 90px;
        ul {
            width: 100%;
            height: 12px;
            line-height: 12px;
            margin-top: 20px;
            margin-left: 37.5%;
            li {
                width: 60px;
                height: 12px;
                border-right: 1px solid #8b8b8b;
                font-size: 12px;
                text-align: center;
            }
        }
        .online {
            width: 100%;
            height: 26px;
            line-height: 26px;
            text-align: center;
            font-size: 12px;
            margin-top: 5px;
        }
        .onlines {
            width: 100%;
            height: 26px;
            line-height: 26px;
            text-align: center;
            font-size: 12px;
        }
    }
}
.show_bind {
    margin: 20px;
    i {
        width: 38px;
        height: 38px;
        border: 1px solid #c4c4c4;
        margin-top: 25px;
        line-height: 38px;
        vertical-align: middle;
        text-align: center;
        background: url(../../assets/img/login_bg.png) no-repeat;
    }
    img {
        margin-top: -5.5px;
    }
    input {
        width: 265px;
        height: 38px;
        border: 1px solid #c4c4c4;
        font-size: 12px;
        margin-top: 25px;
        text-indent: 1em;
    }
    .mob {
        margin-top: 0;
    }
    .abtain {
        width: 90px;
        height: 24px;
        line-height: 24px;
        border-radius: 10px;
        border: 1px solid #c4c4c4;
        font-size: 12px;
        color: #a9a9a9;
        text-align: center;
        position: absolute;
        top: 164px;
        left: 246px;
        cursor: pointer;
    }
    .active {
        background: #c4c4c4;
        color: #fff;
    }
    .loginIn {
        width: 100%;
        background: #ff6000;
        color: #ffffff;
        border: 0;
        margin-top: 40px;
    }
}
</style>
