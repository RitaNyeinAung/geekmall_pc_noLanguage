<template>
    <div class="commission l">
        <el-table :data="pageData" style="width: 100%">
            <el-table-column prop="shop_name" label="店铺名称" width="180">
            </el-table-column>
            <el-table-column prop="money" label="提现金额" width="180">
            </el-table-column>
            <el-table-column prop="type" label="提现类型"> </el-table-column>
            <el-table-column label="状态">
                <template slot-scope="scope">
                    <span v-if="scope.row.status == 0">待审核</span>
                    <span v-if="scope.row.status == 1">已打款</span>
                    <span v-if="scope.row.status == 2">审核不通过</span>
                </template>
            </el-table-column>
        </el-table>
        <el-pagination
            @size-change="handleSizeChange"
            @current-change="handleCurrentChange"
            :current-page="currentPage"
            :page-sizes="[10, 15, 20]"
            :page-size="pagesize"
            layout="total, sizes, prev, pager, next, jumper"
            :total="distributeDetails.length"
        >
        </el-pagination>
    </div>
</template>

<script>
export default {
    data() {
        return {
            Isactive: "",
            distributeDetails: [],
            currentPage: 1, //初始页
            pagesize: 10, //    每页的数据
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
        this.getMoneyDetail();
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
    computed: {
        pageData() {
            return this.distributeDetails.slice(
                (this.currentPage - 1) * this.pagesize,
                this.currentPage * this.pagesize
            );
        }
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
        getMoneyDetail() {
            this.HTTP(this.$httpConfig.withdrawDetail, {})
                .then(res => {
                    this.distributeDetails = res.data.data;
                })
                .catch(err => {
                    console.error(err);
                });
        },
        handleSizeChange(size) {
            this.pagesize = size;
        },
        handleCurrentChange(currentPage) {
            this.currentPage = currentPage;
        }
    }
};
</script>
<style lang="less">
.el-pagination__total {
    display: none !important;
}
.el-pagination__sizes {
    display: none !important;
}
.el-pagination__jump {
    display: none !important;
}
.el-pagination {
    text-align: center;
    margin-top: 20px;
}

.el-pager {
    .number {
        border-radius: 0;
        margin: 0 3px;
    }

    li.active {
        background-color: #d02629;
        color: #fff !important;
        border-radius: 0;
    }
}
</style>
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

.commission {
    width: 980px;
    background: #fff;
    margin: 16px 0;
    padding-bottom: 40px;
    height: 357px;
    .top {
        height: 37px;
        border-bottom: 1px solid #e7e7e7;
        margin: 14px 17px 23px;
        li {
            cursor: pointer;
            float: left;
            width: 104px;
            text-align: center;
            height: 37px;
            font-size: 14px;
            color: #666;
            p {
                line-height: 16px;
                border-right: 1px solid #eee;
                margin-top: 10px;
            }
        }
        li:last-child {
            p {
                border-right: 0;
            }
        }
    }
    .active {
        border-bottom: 2px solid #d02629;
        color: #d02629 !important;
    }
    .under {
        margin: 23px 17px 0;
        border: 1px solid #e7e6e6;
        overflow: hidden;
        .thead {
            overflow: hidden;
            height: 38px;
            background: #f5f5f5;
            line-height: 38px;
        }
        .same {
            overflow: hidden;
            line-height: 38px;
            border-top: 1px solid #e7e6e6;
        }
        p {
            font-size: 12px;
            color: #333;
            float: left;
            text-align: center;
        }
        p:nth-of-type(1) {
            width: 87px;
            margin-right: 147px;
        }
        p:nth-of-type(2) {
            margin-left: 128px;
            width: 104px;
        }
        p:nth-of-type(3) {
            margin-left: 47px;
            width: 105px;
        }
        p:nth-of-type(4) {
            margin-left: 61px;
            width: 127px;
        }
        p:nth-of-type(5) {
            width: 106px;
        }
        .sames {
            p {
                color: #d02629 !important;
            }
        }
    }
}
</style>
