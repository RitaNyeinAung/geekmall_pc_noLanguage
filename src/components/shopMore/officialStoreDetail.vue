<template>
	<div class="officialStoreDetail">
		<common-header></common-header>
		<head-com></head-com>
		<div class="center">
            <div class="OS-body">
                <div v-for="(item,index) in pinpais.records" :key="index" class="sale_product" @click="OSGoods(item.id)">
                    <div class="pro_img">
                        <img class="image" :src="URL + item.store_logo" />
                    </div>
                    <div class="OS-foot">
                        <div class="foot-div">
                            <img :src="URL + item.store_logo" class="foot-img"/>
                        </div> 
                        <div class="foot-name">{{item.shop_name}}</div>
                        <div class="foot-nick">{{item.shop_name}}</div>
                    </div>
                </div>
            </div>
            <div
                style="width: 140px;margin: 0 auto;margin-bottom: 20px"
            >
                <el-pagination
                    @current-change="handleCurrentChange"
                    background
                    layout="total,prev, pager, next,jumper"
                    :current-page.sync="currentPage"
                    :page-size="page_size"
                    :total="page"
                >
                </el-pagination>
            </div>
		</div>
		<foot-com></foot-com>
	</div>
</template>

<script>
	import qs from 'qs';
	export default {
		data() {
			return {
                pinpais: [],
                currentPage: 1,
                page_size: 0,
                page: 0,
        };
    },
		created() {
			this.getFootData();
			this.getFavIcon();
            this.getStoreList();
		},
        mounted(){
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
            getStoreList() {
                this.HTTP(this.$httpConfig.getStoreList, {
                    page: this.currentPage
                }, 'post').then((res) => {
                this.pinpais = res.data.data;
                this.page = Number(res.data.data.count);
                this.page_size = Number(res.data.data.page_size);
                // window.scrollTo(0, 255);
                });
            },
            OSGoods(goods_id) {
                this.$router.push({
                    path: "storehome",
                    query: {
                        id: goods_id
                    }
                });
            },
            handleCurrentChange(currentPage) {
                this.currentPage = currentPage;
                this.getStoreList();
            },
		}
	}
</script>

<style lang="less" scoped>
	.l {
		float: left;
	}
	
	.r {
		float: right;
	}
	
	.officialStoreDetail {
		box-sizing: border-box;
		.center {
			width: 1200px;
			margin: 0 auto;
			height: 100%;
            .OS-body {
                height: auto;
                overflow: hidden;
                padding: 20px 0;
                .sale_product {
                    height: 275px;
                    width: 185px;
                    float: left;
                    margin-right: 14.2px;
                    margin-bottom: 20px;
                    background-color: transparent;
                    cursor: pointer;
                    .pro_img {
                        width: 185px;
                        height: 185px;
                        margin-bottom: 4px;
                        .image {
                            width: 185px;
                            height: 185px;
                        }
                    }
                    .OS-foot{
                        background-color: #f1f1f1;
                        // z-index: 1;
                        // position: absolute;
                        // height: 88px;
                        .foot-div{
                            box-shadow: 0 0 4px 0 rgb(0 0 0 / 25%);
                            width: 52px;
                            height: 52px;
                            background-color: #fff;
                            padding: 4px;
                            position: relative;
                            margin: -25px auto 0;
                            overflow: hidden;
                        .foot-img{
                            text-align: center;
                            width: 44px;
                            height: 44px;
                        }
                        }
                        .foot-name{
                            font-size: 14px;
                            color: #212121;
                            line-height: 18px;
                            margin: 8px 8px 4px;
                            text-align: center;
                        }
                        .foot-nick{
                            font-size: 12px;
                            line-height: 14px;
                            color: #9e9e9e;
                            margin: 0 8px;
                            text-align: center;
                            padding-bottom: 12px;
                        }
                    }
                }
                .sale_product:last-child{
                margin-right: 0; 
                }
                .sale_product:hover {
                    box-shadow: 0 2px 4px 0 rgba(0,0,0,.25);
                } 
            }
		}
	}

</style>