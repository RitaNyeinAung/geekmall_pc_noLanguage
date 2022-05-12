<template>
	<div class="integralGoodsDetail">
		<common-header></common-header>
		<head-com></head-com>
		<div class="center">
			<div class="Integralg">
                <div v-for="(item,index) in IntegralGoodsNewList" :key="index" class="sale_product">
                    <div class="pro_img">
                        <img class="image" :src="URL + item.pic_url"
                            @click="
                            $router.push({
                            name: 'IntegralInside',
                            query: {
                                goods_id: item.goods_id,
                                id: item.id,
                                integral: item.integral
                            }
                            })
                        "/>
                    </div>
                    <p
                        class="jieshao"
                        @mouseenter="updateXY(item.title, $event)"
                        @mouseleave="updataFullFlag"
                        @click="
                        $router.push({
                        name: 'IntegralInside',
                        query: {
                            goods_id: item.goods_id,
                            id: item.id,
                            integral: item.integral
                        }
                        })
                    "
                    >
                    {{ item.title }}
                    </p>
                <div class="bottom">
                <p class="l">
                    价格
                    <span>￥{{ item.price_member }}</span>
                </p>
                <p class="r">
                    <span
                    @click="
                        $router.push({
                        name: 'Settlement',
                        params: { id: item.id,num: 1 }
                        })
                    "
                    style="color: white;"
                    >立即兑换</span
                    >
                </p>
                <p class="l">积分兑换：{{ item.integral }}积分</p>
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
				IntegralGoodsNewList: [],
				currentPage: 1,
                page_size: 0,
                page: 0,
				fullTitle: "",
				displayTitleStyle: {
					top: "",
					bottom: ""
				},
				fullTitieFlag: false,
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
				this.HTTP(this.$httpConfig.getIntegralGoodsNewList, {}, "post")
					.then(res => {
					this.IntegralGoodsNewList = res.data.data;
					this.page = Number(res.data.data.last_page);
                	this.page_size = Number(res.data.data.per_page);
					})
					.catch(e => {
					console.log(e);
					});
			},
			handleCurrentChange(currentPage) {
                this.currentPage = currentPage;
                this.getStoreList();
            },
			updateXY(title, e) {
				this.fullTitle = title;
				this.displayTitleStyle.top = e.pageY + 30 + "px";
				this.displayTitleStyle.left = e.pageX + 20 + "px";
				this.fullTitieFlag = true;
			},
			updataFullFlag() {
				this.fullTitieFlag = false;
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
	
	.integralGoodsDetail {
		box-sizing: border-box;
		.center {
			width: 1200px;
			margin: 0 auto;
			height: 100%;
			.Integralg {
				height: auto;
                overflow: hidden;
				padding: 20px 0;
				.sale_product {
					height: 300px;
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
					.jieshao {
						height: 36px;
						// width: 147px;
						line-height: 18px;
						margin: 0 8px;
						font-size: 12px;
						color: #656565;
						display: -webkit-box;
						overflow: hidden;
						white-space: normal !important;
						text-overflow: ellipsis;
						word-wrap: break-word;
						-webkit-line-clamp: 2;
						-webkit-box-orient: vertical;

						&:hover {
							color: red;
						}
					.first-name{
						color: #ffffff;
						background-color: #de2d35;
						font-size: 12px;
						border-radius: 3px;
						padding: 0 8px 1px 8px;
					}
					}

					.jiage {
						margin: 12px 0 0 8px;
						color: #f64e4d;
						font-size: 13px;
					}
					.bottom {
						// width: 218px;
						height: 54px;
						margin: 0 1px;
						// margin-top: 18px;
						p:nth-of-type(1) {
						font-size: 12px;
						color: #b4b4b4;
						margin-top: 13px;
						margin-left: 8px;
						span {
							text-decoration: line-through;
						}
						}
						p:nth-of-type(2) {
						margin-top: 8px;
						margin-right: 8px;
						width: 68px;
						height: 28px;
						background: #de414c;
						text-align: center;
						line-height: 28px;
						border-radius: 3px;
						cursor: pointer;
						a {
							font-size: 12px;
							color: #fff;
						}
						}
						p:nth-of-type(3) {
						font-size: 13px;
						color: #e64242;
						margin-left: 8px;
						display: -webkit-box;
						overflow: hidden;
						white-space: normal !important;
						text-overflow: ellipsis;
						word-wrap: break-word;
						-webkit-line-clamp: 1;
						-webkit-box-orient: vertical;
						margin-top: 5px;
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