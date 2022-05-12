<template>
	<div class="newProductDetail" id="newProductDetail" ref="newProductDetail">
		<common-header></common-header>
		<head-com></head-com>
		<div class="center">
			<div class="Guess-body">
                <div v-for="(item,index) in newProduct" :key="index" class="sale_product" @click="entryGoods(item)">
                    <div class="pro_img">
                        <img class="image" :src="URL + item.pic_url" />
                    </div>
                    <p
                        class="jieshao"
                        @mouseenter="updateXY(item.title, $event)"
                        @mouseleave="updataFullFlag"
                    >
                    <el-dropdown v-if="item.name">
                        <span class="first-name">{{ item.name }}</span>
                        <el-dropdown-menu slot="dropdown" v-if="item.classification">
                        <el-dropdown-item>{{item.classification}}</el-dropdown-item>
                        </el-dropdown-menu>
                    </el-dropdown>
                    &nbsp;{{ item.title }}
                    </p>
                    <div class="jiage l">
                        ￥
                        <span>{{ item.price_member }}</span>
                        <div
                            style="margin-left:40px;display:inline-block;font-size:12px;color:#656565"
                        >
                            已售{{ item.sales_sum }}件
                        </div>
                    </div>
                </div>
            </div>
			<div class="more">
                <span v-if="floorIndex > 0 && floorIndex <= floorCount" @click="getNewArrivals">加载更多...</span>
                <span v-else class="isEnd"></span
                >
            </div>
			<!-- <div
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
            </div> -->
		</div>
		<foot-com></foot-com>
	</div>
</template>

<script>
	import qs from 'qs';
	import $ from "jquery";
	export default {
		data() {
			return {
				newProduct:[],
				currentPage: 1,
                // page_size: 0,
                // page: 0,
				floorIndex: 1,
				floorCount: null,
				isBottom: false,
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
			this.floorCount = localStorage.getItem('countF')
			this.getNewArrivals();
		},
        mounted(){
			var that = this;
			$(window).scroll(function() {
				if (!that.$refs.newProductDetail) return;
				var bot = 700;
				if (
					that.isBottom == false &&
					bot + $(window).scrollTop() >=
						$(document).height() - $(window).height()
				) {
					that.isBottom = true;
					if (that.floorIndex >0) {
						that.floorIndex++;
						that.getNewArrivals();
					}
				}
			});
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
			// getNewArrivals() {
            //     this.HTTP(
            //         this.$httpConfig.newArrivals, {
			// 			page: this.currentPage
			// 		}, "post")
            //         .then(res => {
            //             this.newProduct = res.data.data;
			// 			this.page = Number(res.data.data.last_page);
            //     		this.page_size = Number(res.data.data.per_page);
            //         })
            //         .catch(e => {
            //             console.log(e);
            //         });
        	// },
			async getNewArrivals() {
				if (this.floorIndex > 0 && this.floorIndex <= this.floorCount) {
					await this.HTTP(
						this.$httpConfig.newArrivals,
						{
							page: this.floorIndex
						},
						"post"
					)
						.then(res => {
							if (res.data.status == 1) {
								if (this.floorIndex == 1) {
									this.newProduct = res.data.data.data;
								} else {
									this.newProduct = [
										...this.newProduct,
										...res.data.data.data
									]
								}
							}
						})
						.catch(e => {
							console.log(e);
						});

					this.isBottom = false;
				}
			},
			// handleCurrentChange(currentPage) {
            //     this.currentPage = currentPage;
            //     this.newArrivals();
            // },
			entryGoods(goods) {
				window.open(
					window.location.origin + "/shopsn_product?id=" + goods.id
				);
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
	
	.newProductDetail {
		box-sizing: border-box;
		.center {
			width: 1200px;
			margin: 0 auto;
			height: 100%;
			.Guess-body {
				height: auto;
                overflow: hidden;
				padding: 20px 0;
				.sale_product {
					height: 280px;
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
				}
				.sale_product:last-child{
				margin-right: 0; 
				}
				.sale_product:hover {
					box-shadow: 0 2px 4px 0 rgba(0,0,0,.25);
				} 
			}
			.more {
				text-align: center;
				height: 48px;
				line-height: 48px;
				color: #f0bf19;
				font-size: 17px;
				font-weight: bold;
				.isEnd{
					display: block;
				}
			}
		}
	}

</style>