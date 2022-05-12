<template>
	<div class="flashSaleDetail">
		<common-header v-on:childToParent="onChildClick"></common-header>
		<head-com v-if="tempid" :navid = tempid></head-com>
        <head-com v-else :navid = 50></head-com>
		<div class="center">
            <div class="box-body">
                <div class="flash_header" id="sticky">
                    <div>
                        <div class="on_sale l" v-if="seckilData == '' || seckilData == null">
                            <div class="sale">活动未开始</div>
                        </div>
                        <div class="on_sale l" v-else>
                            <div class="sale">活动进行中</div>
                        </div>
                        <div class="sale_end l" v-if="seckilData == '' || seckilData == null">
                        </div>
                        <div class="sale_end l" v-else>
                            <div class="end_time">剩余时间</div>
                            <div class="end_hour l">{{showTime}}</div>
                        </div>
                        <div class="time_length l">
                            <!-- <div @click="showActive(item.id, index)" class="spr-time l" :class="{ active: itemIndex == index }" v-for="(item, index) in timeData" :key="index"> -->
                            <div class="spr-time l" v-for="(item, index) in dateTime" :key="index">
                                <p class="spr-hor">{{item.startTime}}</p>
                                <p class="spr-btn">{{item.name}}</p>
                            </div>
                        </div>
                        <!-- <div class="sale_time r">01:30 Tomorrow</div> -->
                    </div>
                </div>
                <div class="flashSale_box">
                    <div v-for="(item,index) in seckilData.data" :key="index" class="sale_product" @click="entryGoods(item.goods_id)">
                        <div class="pro_img">
                            <img class="image" :src="URL+item.pic_url" />
                        </div>
                        <div class="pro_text">
                            <div class="pro_title">{{item.title}}</div>
                            <div class="sold-pho">
                                <div class="sold-show" :style="'width:' + ((item.buy_num/item.activity_num)*100) + '%'"></div>
                            </div>
                            <div class="sold_instock">
                                <div class="sold_num">已抢购{{item.buy_num}}件</div>
                                <div class="instock_num">仅剩{{item.activity_num - item.buy_num}}件</div>
                            </div>
                            <div class="pro-foot">
                                <div class="l">
                                    <div class="pro_price">￥{{item.activity_price}}</div>
                                    <div class="price_sale">
                                        <span class="origin_price">￥{{item.price_member}}</span>
                                        <span class="discount_price">-{{item.activity_num}}%</span>
                                    </div>
                                </div>
                                <div class="btn-pro r">查看产品</div>
                            </div>
                        </div>
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
    import $ from 'jquery';
	export default {
		data() {
			return {
                fromChild: '',
                tempid: '',
                itemIndex: 0,
                timeData: [],
                timeId: '',
                seckilData:[],
                currentPage: 1,
                page_size: 0,
                page: 0,
                showTime: 0,
                countDownTime:0,
                showTimeData: '',
                timeDataFilter: [],
                dateTime: [],
        };
    },
		created() {
			this.getFootData();
			this.getFavIcon();
            this.getTimeList();
		},
        mounted(){
            this.tempid = this.$route.query.id
            let sticky = $('#sticky'),
            stickyTop = sticky.offset().top,
            scrolled = false,
            $window = $(window);

            /* Bind the scroll Event */
            $window.on('scroll', function(e) {
                scrolled = true;
            });

            let timeout = setInterval(function() {
                /* If the page was scrolled, handle the scroll */
                if (scrolled) {
                    scrolled = false;
                    if ($window.scrollTop() >= stickyTop) {
                        sticky.addClass('fixed');
                        // console.log("sticky");
                    }
                    else {
                    sticky.removeClass('fixed');
                    }
                }
            }, 200);
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
            countDownTimer() {
                if(this.countDownTime > 0) {
                    setTimeout(() => {
                        this.countDownTime -= 1;
                        var h = Math.floor(this.countDownTime / 3600);
                        var m = Math.floor(this.countDownTime % 3600 / 60);
                        var s = Math.floor(this.countDownTime % 3600 % 60);

                        var hDisplay = h > 0 ? h + (h == 1 ? "时: " : "时: ") : 0 + "时: ";
                        var mDisplay = m > 0 ? m + (m == 1 ? "分：" : "分：") : 0 + "分：";
                        var sDisplay = s > 0 ? s + (s == 1 ? "秒" : "秒") : 0 + "秒";
                        this.showTime = hDisplay + mDisplay + sDisplay;
                        this.countDownTimer()
                    }, 1000)
                }
            },
            // getTimeList() {
            //     this.HTTP(this.$httpConfig.timeList, {}, "post")
            //         .then(res => {
            //             this.timeData = res.data.data;
            //             this.timeId = res.data.data[0].id;
            //             this.getseckillList(this.timeId);
            //         })
            //         .catch(err => {
            //             console.log(err);
            //         });
            // },
            getTimeList() {
                this.HTTP(this.$httpConfig.timeList, {}, "post")
                    .then(res => {
                        this.timeData = res.data.data;
                        var now = Math.round(new Date().getTime() / 1000);
                        this.showTimeData = now;

                        this.timeData.forEach((time, index) => {
                            if(time.countdown) {
                                this.countDownTime = time.countdown;
                                this.timeId = time.id;
                            } 
                        })

                        var data = this.timeData.filter(time=> {
                            return !(time.end_time<this.showTimeData)
                        }).filter(time => {
                            return !(this.showTimeData>time.start_time&&this.showTimeData<time.end_time)
                        }).slice(0,4)
                        this.timeDataFilter = data;

                        var m = new Date();
                        var dateString =
                            m.getUTCFullYear() + "-" +
                            ("0" + (m.getUTCMonth()+1)).slice(-2) + "-" +
                            ("0" + m.getUTCDate()).slice(-2);

                        var date = this.timeDataFilter.map(item => {
                            var starDate = item.start.split(" ")[0];
                            var startTime = item.start.split(" ")[1];
                            
                            var day1 = new Date(starDate); 
                            var day2 = new Date(dateString);
                            var difference= Math.abs(day2-day1);
                            var diffDays = Math.ceil(difference / (1000 * 60 * 60 *24));

                            if(diffDays == 0) {
                                item.startTime = startTime
                                item.name = '今天'
                                return item;
                            }
                            if(diffDays == 1) {
                                item.startTime = startTime
                                item.name = '明天'
                                return item;
                            }
                            if(diffDays == 2) {
                                item.startTime = startTime
                                item.name = '后天'
                                return item;
                            }
                            if(diffDays > 2) {
                                item.startTime = startTime
                                item.name = starDate
                                return item;
                            }
                        })
                        this.dateTime = date;

                        this.countDownTimer();
                        this.getseckillList(this.timeId);
                    })
                    .catch(err => {
                        console.log(err);
                    });
            },
            entryGoods(id) {
                window.open(
                window.location.origin +
                    "/flash_product?id=" + id
            );
			},
            showActive(id, index) {
                this.itemIndex = index;
                this.getseckillList(id);
            },
            getseckillList(id) {
                this.HTTP(this.$httpConfig.getSeckillList, {
                    id: id,
                    page: this.currentPage
                }, "post")
                    .then(res => {
                        this.seckilData = res.data.data;
                        this.page = Number(res.data.data.last_page);
                		this.page_size = Number(res.data.data.per_page);
                    })
                    .catch(err => {
                        console.log(err);
                    });
            },
            handleCurrentChange(currentPage) {
                this.currentPage = currentPage;
                this.getseckillList();
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
	
	.flashSaleDetail {
		box-sizing: border-box;
		.center {
			width: 1200px;
			margin: 0 auto;
			height: 100%;
            .box-body {
                width: 1200px;
                height: auto;
                overflow: hidden;
                // padding: 20px 0;
                .fixed {
                    position:fixed; 
                    top:0px;
                }
                .flash_header {
                    width: 1200px;
                    overflow: hidden;
                    border-top: 1px solid #d5d5d5;
                    border-bottom: 1px solid #d5d5d5;
                    height: 60px;
                    line-height: 60px;
                    margin-bottom: 20px;
                    background: #fff;
                    z-index: 11111;
                    .on_sale {
                        margin-left: 20px;
                        .sale {
                            font-weight: 500;
                            font-size: 14px;
                            color: #d02629;
                            cursor: pointer;
                        }
                    }
                    .sale_end {
                        overflow: hidden;
                        margin-left: 50px;
                        display: flex;
                        .end_time {
                            font-size: 14px;
                            margin-right: 10px;
                            color: #202020;
                        }
                        .end_hour {
                            background-color: #d02629;
                            border-radius: 2px;
                            font-weight: 500;
                            margin: 12px 6px;
                            font-size: 16px;
                            color: #fff;
                            text-align: center;
                            padding: 0 10px;
                            height: 35px;
                            line-height: 35px;
                        }
                        .space {
                            color: #d02629;
                        }
                    }
                    .time_length {
                        // width: 700px;
                        display: flex;
                        flex-direction: row;
                        overflow-y: hidden;
                        flex-wrap: nowrap;
                        height: 72px;
                        .spr-time{
                            margin-left: 25px;
                            // display: flex;
                            display: -webkit-box;
                            cursor: pointer;
                            .spr-hor{
                                font-size: 14px;
                                // color: #333;
                                font-weight: bold;
                            }
                            .spr-btn{
                                font-size: 12px;
                                color: #999;
                                border: 1px solid #999;
                                border-radius: 15px;
                                padding: 1px 8px;
                                height: 30px;
                                line-height: 25px;
                                margin: 15px 25px 15px 5px;
                            }
                        }
                        .active {
                            color: #d02629;
                        }
                        .spr-time:hover {
                            .spr-hor{
                                font-size: 14px;
                                color: #d02629;
                                font-weight: bold;
                            }
                        }
                    }
                    .sale_time{
                        font-size: 14px;
                        cursor: pointer;
                        padding-right: 20px;
                    }
                }
                .flashSale_box {
                    height: auto;
                    .sale_product {
                        height: 320px;
                        width: 188px;
                        float: left;
                        margin-right: 14.2px;
                        margin-bottom: 20px;
                        background-color: transparent;
                        cursor: pointer;
                        .pro_img {
                            width: 188px;
                            height: 188px;
                            margin-bottom: 4px;
                            .image {
                                width: 188px;
                                height: 188px;
                            }
                        }
                        .pro_text {
                            margin: 0 8px;
                            .pro_title {
                                height: 36px;
                                line-height: 18px;
                                display: -webkit-box;
                                -webkit-box-orient: vertical;
                                -webkit-line-clamp: 2;
                                overflow: hidden;
                                margin-bottom: 4px;
                                font-size: 12px;
                                color: #656565;
                            }
                            .sold-pho{
                                width: 100%;
                                height: 6px;
                                background: #f1f1f1;
                                display: inline-block;
                            }
                            .sold-show{
                                height: 6px;
                                // position: absolute;
                                background-color: #d02629;
                            }
                            .sold_instock {
                                display: flex;
                                font-size: 12px;
                                color: #656565;
                                align-items: center;
					            justify-content: space-between;
                                .instock_num{
                                    color:#d02629;
                                }
                            }
                            .pro-foot{
                                // display: flex;
                            }
                            .btn-pro{
                                margin-top: 8px;
                                width: 68px;
                                height: 28px;
                                background: #d02629;
                                color: #fff;
                                text-align: center;
                                line-height: 28px;
                                border-radius: 3px;
                                cursor: pointer;
                            }
                            .pro_price {
                                margin-bottom: 4px;
                                font-size: 14px;
                                color: #d02629;
                                letter-spacing: -.56px;
                                line-height: 22px;
                                height: 22px;
                            }
                            .price_sale {
                                font-size: 12px;
                                line-height: 12px;
                                margin-bottom: 4px;
                                .origin_price {
                                    color: #9e9e9e;
                                    text-decoration: line-through;
                                }
                                .discount_price {
                                    color: #212121;
                                    margin-left: 4px;
                                }
                            }
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