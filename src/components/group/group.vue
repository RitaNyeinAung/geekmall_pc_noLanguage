<template>
	<div class="brand">
		<common-header v-on:childToParent="onChildClick"></common-header>
		<head-com v-if="tempid" :navid = tempid></head-com>
		<div class="banner">
            <div class="banner-center">
                <div class="banner-img">
					<img src="../../assets/img/background.jpg" />
                    <!-- <el-carousel
                        :interval="5000"
                         arrow="always"
                        :height="bannerHeight"
                    >
                        <el-carousel-item v-for="item in banner" :key="item.id">
                            <a :href="item.ad_link" style="pointer-events:auto; ">
                                <img :src="URL + item.pic_url" />
                            </a>
                        </el-carousel-item>
                    </el-carousel> -->
                </div>
            </div>
        </div>
		<div class="center">
			<!-- <div class="top">
				<el-breadcrumb separator-class="el-icon-arrow-right">
					<el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
					<el-breadcrumb-item :to="{ path: '/brand' }">品牌</el-breadcrumb-item>
				</el-breadcrumb>
			</div> -->
			<!-- <ul class="bottom">
				<li :key="index" v-for="(item,index) in recommandList" class="recommand">
					
					<div @click="innerbrand(item)">
						<img v-if="item .recommend==1" src="../../assets/img/recommandBrand.png" class="tuijian">
						<img class="bann-img" :src="URL + item.brand_banner" />
						<img class="logo-img" :src="URL + item.brand_logo" />
						<p>{{item.brand_name}}</p>
					</div>
				</li>
			</ul> -->
			<ul class="bottom">
				<li :key="index" v-for="(item,index) in promotionList">
					<div @click="assembleProduct(item)">
						<img class="bann-img" :src="URL + item.pic_url" />
						<div class="assemble">
							<div class="assemble-title">{{item.title}}</div>
							<div class="assemble-price">￥{{item.activity_price}}</div>
							<div class="progress-line">
								<div class="progress-barr">
									<span :style="{width:(item.buy_num/item.activity_num)*100+'%'}"></span>
								</div>
								<div>{{item.percentage}}%</div>
							</div>
							<div class="bottom-line">
								<div class="num">{{item.buy_num}}人已拼单</div>
								<div class="status" v-if="item.status == 1">拼团中</div>
								<div class="status" v-if="item.status == 0">去开团</div>
							</div>
						</div>
					</div>
				</li>
			</ul>
			<div style="width: 140px;margin: 20px auto">
				<el-pagination
					@current-change="
						handleCurrentChange
					"
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
				tempid: '',
				banner: [],
				bannerHeight: "500px",
				page: 1,
				// recommandList: [],
				promotionList: [],
				list: [],
				currentPage: 1,
				page_size: 0,
				page: 0,
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
			// this.getRecommandBrand();
			this.getGroupPurchase();
			this.getFootData();
			this.getFavIcon();
		},
        mounted(){
			this.tempid = this.$route.query.id;
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
			onChildClick (value) {
				this.fromChild = value
				if(this.fromChild == 'false') {
					location.reload();
				}
        	},
			// getRecommandBrand() {
			// 	this.HTTP(this.$httpConfig.recommendBrand, {}, 'post')
			// 		.then((res) => {
			// 			this.recommandList = res.data.data;
			// 		})
			// },
			handleCurrentChange(currentPage) {
				this.currentPage = currentPage;
				this.getGroupPurchase();
			},
			getGroupPurchase() {
				this.HTTP(this.$httpConfig.groupPurchase, {
					page: this.currentPage
					}, 'post')
					.then((res) => {
						this.promotionList = res.data.data.data;
						this.page = Number(res.data.data.last_page);
                    	this.page_size = Number(res.data.data.per_page);
					})
			},
			// innerbrand(item) {
			// 	this.$router.push({
			// 		name: 'innerbrand',
			// 		params: {
			// 			id: item.id
			// 		}
			// 	})
			// },
			assembleProduct(item) {
				window.open(
                window.location.origin + "/innerGroup?id=" + item.goods_id +"&goods_id=" + item.id
            );
			}
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

	.brand {
		box-sizing: border-box;
		.banner {
			width: auto;
			// height: 500px;

			.banner-center {
				width: 100%;
				height: 100%;
				position: relative;
				.banner-img {
					top: 0;
					left: 0;
					img {
						width:100%;
						height:100%;
					}
				} 
			}
		}
		.center {
			width: 1200px;
			margin: 0 auto;
			height: 100%;
			margin-top: 30px;
	
		// .top {
		// 	font-size: 12px;
		// 	margin: 15px 0;
		// 	img {
		// 		margin: 12px 8px 0 0;
		// 		float: left;
		// 	}
		// 	span:last-child {
		// 		color: #616161 !important;
		// 	}
		// 	span {
		// 		color: #a7a7a7;
		// 	}
		// }
		// .bottom {
		// 	width: 1200px;
		// 	height: auto;
		// 	background: #f1f1f1;
		// 	margin-bottom: 30px;
		// 	display: flex;
		// 	flex-direction: row;
		// 	flex-wrap: wrap;
		// 	justify-content: flex-start;
		// 	box-sizing: border-box;
		// 	padding: 5px;
		// 	li {
		// 		height: 420px;
    	// 		width: 260px;
		// 		border: 1px solid #e5e4df;
		// 		margin: 15px 20px 15px 17px;
		// 		box-sizing: border-box;
		// 		position: relative;
		// 		background-color: #fff;
		// 		.tuijian{
		// 			position: absolute;
		// 			width: 40px;
		// 		}
		// 		.bann-img {
		// 			width: 270px;
    	// 			height: 270px;
					
		// 		}
		// 		.logo-img {
		// 			height: 70px;
		// 			width: 200px;
		// 			margin: 18px 29px 15px 29px;
		// 		}
		// 		p {
		// 			border-top: 1px solid #eee;
		// 			width: 255px;
		// 			height: 30px;
		// 			margin: 0 auto;
		// 			line-height: 50px;
		// 			text-align: center;
		// 			color: #666;
		// 		}
		// 	}
		// }
		.bottom {
			width: 1200px;
			height: auto;
			background: #f1f1f1;
			margin-bottom: 30px;
			display: flex;
			flex-direction: row;
			flex-wrap: wrap;
			justify-content: flex-start;
			box-sizing: border-box;
			padding: 5px;
			li {
				height: 420px;
    			width: 260px;
				border: 1px solid #e5e4df;
				margin: 15px 20px 15px 17px;
				box-sizing: border-box;
				position: relative;
				background-color: #fff;
				cursor: pointer;
				.bann-img {
					width: 260px;
    				height: 260px;
				}
				.assemble {
					padding: 0 15px;
					.assemble-title {
						width: 230px;
						height: 43px;
						overflow: hidden;
						color: #333;
					}
					.assemble-price {
						color: #d02629;
						font-size: 16px;
						margin-top: 5px;
					}
					.progress-line {
                        display: flex;
                        align-items: center;
						margin-top: 5px;
                        .progress-barr {
                            width: 185px;
                            height: 8px;
                            background: #e9e9e9;
                            border-radius: 5px;
                            > span {
                                display: block;
                                width: 0%;
                                background: #d02629;
                                border-radius: 5px;
                                height: 8px;
                            }
                        }
                        > div:nth-child(2) {
                            color: #6a6a6a;
                            font-size: 16px;
                            margin-left: 10px;
                        }
                    }
					.bottom-line{
                        display: flex;
                        align-items: center;
                        justify-content: space-between;
                        margin-top: 5px;
                        >div:nth-child(1){
                            color: #6a6a6a;
                            font-size: 16px;
                        }

                        .num {
                        font-size: 16px;
						}
						.status {
							display : flex;
							color: #6a6a6a;
							font-size: 16px;
						}
                    }
				}
			}
		}
	}
}
</style>