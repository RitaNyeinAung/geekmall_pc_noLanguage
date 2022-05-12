<template>
    <div class="my-charge l">
        <div class="top">
            <span class="l">充值记录</span><span class="r btncss" @click="gotoRecharge">在线充值</span>
        </div>

        <p class="l inp">
            充值类型：
            <el-select
                v-model="type_id"
                placeholder="请选择"
                @change="handleSelectChange"
            >
                <el-option
                    v-for="item in rechargeType"
                    :key="item.id"
                    :label="item.type_name"
                    :value="item.id"
                >
                </el-option>
            </el-select>
        </p>

        <p class="l inp">
            充值日期：
            <el-date-picker
                v-model="value"
                type="datetimerange"
                value-format="yyyy-MM-dd HH:mm:ss"
                range-separator="至"
                start-placeholder="开始日期"
                end-placeholder="结束日期"
            >
            </el-date-picker>
        </p>

        <p @click="search" class="l search">搜索</p>

        <ul>
            <li @click="Chongzhi(null)" :class="{ line: Line === null }">
                全部记录
            </li>

            <li @click="Chongzhi(1)" :class="{ line: Line === 1 }">
                已支付记录
            </li>

            <li @click="Chongzhi(0)" :class="{ line: Line === 0 }">
                未支付记录
            </li>
        </ul>

        <div class="under">
            <div class="thead">
                <p>充值时间</p>
                <p>充值金额(元)</p>
                <p>留言</p>
                <p>状态</p>
                <p>支付方式</p>
                <p>编辑</p>
            </div>

            <div
                class="down"
                v-for="(item, index) in rechargeList"
                :key="index"
            >
                <p>{{ item.ctime | formatDate }}</p>
                <p>
                    <span>{{ item.account }}</span>
                </p>
                <p>{{ item.remark }}</p>
                <p v-if="item.pay_id === 5">{{ offlineStatus[item.pay_status] }}</p>
                <p v-else>{{ rechargeStatus[item.pay_status] }}</p>
                <p>{{ item.type_name }}</p>
                <p>
                    <el-button
                        size="mini"
                        v-if="item.pay_status == 0"
                        @click="handleDelete(item.id, index)"
                        >删除</el-button
                    >
                    <el-button
                        size="mini"
                        v-if="item.pay_status == 0"
                        @click="handlePay(item.pay_id, item.account)"
                        >支付</el-button
                    >
                </p>
            </div>
        </div>

        <div class="fenye" v-show="page >= 15">
            <div class="box2">
                <el-pagination
                    @current-change="handleCurrentChange"
                    background
                    layout="total,prev, pager, next,jumper"
                    :page-size="page_size"
                    :total="page"
                >
                </el-pagination>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            value: null,
            rechargeList: [],
            rechargeStatus: ["待支付", "充值成功", "交易关闭"],
            offlineStatus: ["待审核", "充值成功"],
            Line: "",
            rechargeType: [],
            type_id: "",
            page: 0, //共多少条
            currentPage: 1, //当前页
            page_size: 15, //每页显示多少条数据
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
        this.getRechargeList();
        this.getRechargeType();
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
        gotoRecharge() {
            this.$router.push("/recharge");
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
        handlePay(id, money) {
            if (id == 1) {
                this.HTTP(
                    this.$httpConfig.payOrderData,
                    {
                        pay_type_id: id,
                        money: money
                    },
                    "post"
                )
                    .then(res => {
                        sessionStorage.setItem(
                            "payData",
                            JSON.stringify(res.data.data)
                        );
                        this.$route.params.total_price = parseInt(money);
                        this.$router.push({
                            name: "wxpay",
                            params: {
                                total_price: parseInt(money),
                                id: id,
                                state: "c"
                            }
                        });
                    })
                    .catch(err => {
                        console.error(err);
                    });
            } else if (id == 2) {
                this.HTTP(
                    this.$httpConfig.payOrderData,
                    {
                        pay_type_id: id,
                        money: money
                    },
                    "post"
                )
                    .then(res => {
                        this.payData = res.data.data;
                        const oDiv = document.createElement("div");
                        oDiv.innerHTML = this.payData;
                        document.body.appendChild(oDiv);
                        document.forms.Alipaysubmit.submit();
                    })
                    .catch(err => {
                        console.error(err);
                    });
            }
        },
        handleDelete(id, index) {
            this.HTTP(this.$httpConfig.recordDelete, {
                id: id
            })
                .then(res => {
                    this.rechargeList.splice(index, 1);
                    this.$message.success(res.data.message);
                })
                .catch(err => {
                    console.error(err);
                });
        },
        //跳转分页
        handleCurrentChange(currentPage) {
            this.currentPage = currentPage;
            this.search();
        },
        handleSelectChange(val) {
        },
        search() {
            // 时间
            let timegp = null;
            if (this.value === null) {
                timegp = null;
            } else {
                timegp = this.value.join(" - ");
            }
            this.HTTP(
                this.$httpConfig.rechargeList + "/p/" + this.currentPage,
                {
                    pay_status: Number(this.Line),
                    pay_id: this.type_id,
                    timegp: timegp
                },
                "post"
            )
                .then(res => {
                    if (res.message == "") {
                        this.$message.error("暂无数据");
                    } else {
                        this.rechargeList = res.data.data.data;
                        this.page = Number(res.data.data.page);
                        this.page_size = Number(res.data.data.page_size);
                    }
                })
                .catch(e => {
                    console.log(e);
                });
        },

        getRechargeList() {
            this.HTTP(this.$httpConfig.rechargeList, {}, "post")
                .then(res => {
                    console.log(res);
                    this.rechargeList = res.data.data.data;
                    this.page = Number(res.data.data.page);
                    this.page_size = Number(res.data.data.page_size);
                })
                .catch(() => {});
        },

        getRechargeType() {
            this.HTTP(this.$httpConfig.getPayRechargeResult, {}, "post")
                .then(res => {
                    this.rechargeType = res.data.data;
                })
                .catch(() => {});
        },

        Chongzhi(index) {
            this.Line = index;
            this.rechargeList = [];
            this.search();
        }
    },

    getpayStates() {
        for (var item of this.rechargeList.data) {
            if (item.pay_status == 0) {
            }
        }
    }
};
</script>

<style lang="less" scoped>
.l {
    float: left;
}

.fenye {
    width: 100%;
    overflow: hidden;

    .box2 {
        display: flex;
        justify-content: center;
        flex-wrap: nowrap;
        width: 23%;
        margin: 0 auto;
    }
}

.r {
    float: right;
}
.btncss {
    cursor: pointer;
}

.center {
    width: 1200px;
    margin: 0 auto;
    height: 100%;
}

.my-charge {
    height: 956px;
    width: 980px;
    background: #fff;
    margin: 16px 0;

    .top {
        overflow: hidden;
        margin: 14px 17px 0px;
        border-bottom: 1px solid #e7e7e7;
        line-height: 37px;

        span.l {
            width: 104px;
            text-align: center;
            border-bottom: 2px solid #d02629;
            font-size: 14px;
            color: #d02629;
        }

        span.r {
            width: 80px;
            height: 28px;
            text-align: center;
            line-height: 28px;
            background: #d02629;
            color: #fff;
            border-radius: 4px;
            margin-top: 4px;
        }
    }

    p.inp {
        margin: 21px 10px 0 18px;

        font-size: 12px;

        color: #333;

        select {
            width: 205px;

            height: 40px;

            outline: none;

            color: #666;
            appearance: none;
            padding: 0px 7px 0 9px;
        }
    }

    p.search {
        width: 54px;
        height: 28px;
        text-align: center;
        line-height: 28px;
        color: #fff;
        border-radius: 4px;
        background: #27a9e3;
        cursor: pointer;
        margin-top: 21px;
    }

    ul {
        overflow: hidden;
        margin: 64px 17px 15px;
        border-bottom: 1px solid #e7e7e7;
        li {
            cursor: pointer;
            float: left;
            line-height: 40px;
            width: 104px;
            text-align: center;
            font-size: 13px;
            color: #666;
        }

        .line {
            border-bottom: 2px solid #d02629;
            color: #d02629;
        }
    }

    .under {
        overflow: hidden;
        border: 1px solid #e7e6e6;
        margin: 0 17px;
        .thead {
            line-height: 42px;
            background: #f1f1f1;
            overflow: hidden;
        }

        p {
            height: 100%;
            text-align: center;
            float: left;
            font-size: 12px;
            color: #333;
        }

        p:nth-of-type(1) {
            width: 178px;
            margin-right: 54px;
        }

        p:nth-of-type(2) {
            width: 127px;
        }

        p:nth-of-type(3) {
            width: 171px;
        }

        p:nth-of-type(4) {
            width: 112px;
            margin-right: 24px;
        }

        p:nth-of-type(5) {
            width: 139px;
        }

        .down {
            line-height: 39px;
            height: 39px;
            border-top: 1px solid #e7e6e6;
            span {
                color: #e31939;
            }
        }
    }
}
</style>
