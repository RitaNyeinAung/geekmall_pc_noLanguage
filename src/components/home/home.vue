<template>
    <div id="home" ref="home">
        <common-header v-on:childToParent="onChildClick"></common-header>
        <head-com :flag="flag" v-on:childToParent="onClick"></head-com>
        
        <div class="center">
            <div class="banner">
                <div class="banner-center">
                    <div class="banner-img">
                        <el-carousel
                            :interval="5000"
                            arrow="always"
                            :height="bannerHeight"
                        >
                            <el-carousel-item v-for="item in banner" :key="item.id">
                                <a :href="item.ad_link" style="pointer-events:auto; ">
                                    <img :src="URL + item.pic_url" />
                                </a>
                            </el-carousel-item>
                        </el-carousel>
                    </div>
                    <div id="apDiv2">
                        <div class="denglu r">
                        <!-- <img class="touxiang" @click="HeadPortrait" v-if="userInfo.userHeaderImg" :src="URL+userInfo.userHeaderImg"/>
                        <img class="touxiang" @click="HeadPortrait" v-else src="../../assets/img/default-head.png"/> -->
                        <div v-if="userInfo.userHeaderImg">
                            <img class="touxiang" @click="HeadPortrait" v-if="userInfo.userHeaderImg.split(':').length == 1" :src="URL+userInfo.userHeaderImg" onerror="this.src='https://avataaars.io/?avatarStyle=Circle&topType=ShortHairShortRound&accessoriesType=Blank&hairColor=Black&facialHairType=Blank&clotheType=BlazerShirt&eyeType=WinkWacky&eyebrowType=Default&mouthType=Default&skinColor=Light'"/>
                            <img class="touxiang" @click="HeadPortrait" v-if="userInfo.userHeaderImg.split(':').length == 2" :src="userInfo.userHeaderImg" onerror="this.src='https://cdn.cloudflare.steamstatic.com/steamcommunity/public/images/avatars/e6/e63035e2c6dad3a014ede599c31cc6d6625b09a5.jpg'"/>
                        </div>
                        <div v-else>
                            <img class="touxiang" @click="HeadPortrait" src="https://avataaars.io/?avatarStyle=Circle&topType=ShortHairShortRound&accessoriesType=Blank&hairColor=Black&facialHairType=Blank&clotheType=BlazerShirt&eyeType=WinkWacky&eyebrowType=Default&mouthType=Default&skinColor=Light" onerror="this.src='https://avataaars.io/?avatarStyle=Circle&topType=ShortHairShortRound&accessoriesType=Blank&hairColor=Black&facialHairType=Blank&clotheType=BlazerShirt&eyeType=WinkWacky&eyebrowType=Default&mouthType=Default&skinColor=Light'"/>
                        </div>
                        <div class="qdl l" v-if="userInfo.userName==null">
                            <span class="qingdenglu both" @click="logIn">请登录</span><br>
                            <router-link to="agreement" class="kaidian both">我要开店</router-link>
                        </div>
                        <div class="qdl l" v-else>
                            <router-link class="qingdenglu both" to='myOrder?id=1'>查看订单</router-link><br>
                            <router-link to="personalData" class="kaidian both">查看资料</router-link>
                        </div>
                        <h3 class="" v-if="userInfo.userName!=null"><span
                            class="user-name text1-hidden">Hi, {{userInfo.userName}}</span></h3>
                        <h3 class="" v-else><span
                            class="user-name text1-hidden">Hi,欢迎来到{{headParams.title}}</span></h3>
                        <h4 class="l">公告</h4>
                        <div class="seamless-warp wufeng l">
                            <ul class="item">
                            <li @click="Announcement(item.id)" v-for="(item,index) in items" :key="index">{{item.title}}</li>
                            </ul>
                        </div>
                        <h4 class="l rukou">快捷入口</h4>
                        <ul class="ul l">
                            <li>
                            <!-- <router-link to="ecentBrowse" > -->
                            <a href="javascript:;" @click="toLink('ecentBrowse')">
                                <i class="el-icon-view"></i><br/> 我的浏览
                            </a>

                            <!-- </router-link> -->
                            </li>
                            <li>
                            <a href="javascript:;" @click="toLink('collect')">
                                <img src="../../assets/img/heart.jpg" style="width: auto; height: auto;" /> 我的收藏
                            </a>
                            <!-- <router-link to="collect"> -->

                            <!-- </router-link> -->
                            </li>
                            <li>
                            <a href="javascript:;" @click="toLink('myOrder',0)">
                                <i class="el-icon-date"></i>
                                <br/>我的订单
                            </a>
                            <router-link to="myOrder?id=1">

                            </router-link>
                            </li>
                        </ul>
                        </div>
                    </div>
                </div>
            </div>
            <!--首页限时抢购-->
            <!-- <div class="wti-sk wrapper">
                <div class="sk_inner">
                    <div class="sk_hd">
                        <img src="../../assets/img/rushbuy.jpg" alt="" />
                    </div>
                    <div class="sk_bd">
                        <div class="sk_list slider_list">
                            <div class="sk_list_inner">
                                <div class="goods_wrapper swiper-container">
                                    <div class="swiper-wrapper">
                                        <div
                                            class="swiper-slide"
                                            v-for="(item, index) in panicList"
                                            :key="index"
                                            @click="toRushBuy(item.goods_id)"
                                        >
                                            <div class="sk_item">
                                                <div class="sk_item_lk">
                                                    <div class="goods-thumb">
                                                        <img
                                                            :src="
                                                                URL +
                                                                    item.pic_url
                                                            "
                                                        />
                                                    </div>
                                                    <p
                                                        class="sk_item_name"
                                                        @mouseenter="
                                                            updateXY(
                                                                item.title,
                                                                $event
                                                            )
                                                        "
                                                        @mouseleave="
                                                            updataFullFlag
                                                        "
                                                    >
                                                        {{ item.title }}
                                                    </p>
                                                    <div class="sk_item_price">
                                                        <span
                                                            class="sk_item_price_new"
                                                            ><span
                                                                >&yen;{{
                                                                    item.panic_price
                                                                }}</span
                                                            ></span
                                                        ><span
                                                            class="sk_item_price_origin"
                                                            ><span
                                                                >&yen;{{
                                                                    item.price_member
                                                                }}</span
                                                            ></span
                                                        >
                                                    </div>
                                                </div>
                                            </div>
                                        </div>
                                    </div>

                                    <div class="swiper-button-prev"></div>
                                    <div class="swiper-button-next"></div>
                                </div>
                            </div>
                        </div> -->
                        <!--<div class="sk_chn">-->
                        <!--<div class="sk_chn_inner">-->
                        <!--<img v-if="panicAd.pic_url" @click="toLink1(panicAd.ad_link)" width="200" height="260"-->
                        <!--:src="URL + panicAd.pic_url">-->
                        <!--</div>-->
                        <!--</div>-->
                    <!-- </div>
                </div>
            </div> -->

            <!-- <div class="wti-sk wrapper">
                <div class="sk_inner">
                    <div class="sk_hd">
                        <img src="../../assets/img/rushbuy.jpg" alt="" />
                    </div>
                    <div class="sk_bd">
                        <div class="sk_list slider_list">
                            <div class="sk_list_inner">
                                <swiper class="swiper" :options="swiperOption">
                                    <swiper-slide
                                        v-for="(item, index) in panicList"
                                        :key="index">
                                        <div class="sk_item" @click="toRushBuy(item.goods_id)">
                                                <div class="sk_item_lk">
                                                    <div class="goods-thumb">
                                                        <img
                                                            :src="
                                                                URL +
                                                                    item.pic_url
                                                            "
                                                        />
                                                    </div>
                                                    <p
                                                        class="sk_item_name"
                                                        @mouseenter="
                                                            updateXY(
                                                                item.title,
                                                                $event
                                                            )
                                                        "
                                                        @mouseleave="
                                                            updataFullFlag
                                                        "
                                                    >
                                                        {{ item.title }}
                                                    </p>
                                                    <div class="sk_item_price">
                                                        <span
                                                            class="sk_item_price_new"
                                                            ><span
                                                                >&yen;{{
                                                                    item.activity_price
                                                                }}</span
                                                            ></span
                                                        ><span
                                                            class="sk_item_price_origin"
                                                            ><span
                                                                >&yen;{{
                                                                    item.price_member
                                                                }}</span
                                                            ></span
                                                        >
                                                    </div>
                                                </div>
                                            </div>
                                    </swiper-slide>
                                    <div class="swiper-button-prev" slot="button-prev"></div>
                                    <div class="swiper-button-next" slot="button-next"></div>
                                </swiper>
                            </div>
                        </div>
                    </div>
                </div>
            </div> -->

            

            <!-- <div class="dianpu">
                <div class="left l">
                   
                    <img class="l" :src="URL + imgStoreLogo" />
                </div>
                <ul class="r">
                    <li
                        class="l right"
                        v-for="(pinpai, index) in pinpais"
                        :key="index"
                    >
                        <img :src="URL + pinpai.store_logo" />
                        <div class="click">
                            <span
                                @click="
                                    $router.push({
                                        path: '/storehome',
                                        query: {
                                            id: pinpai.id
                                        }
                                    })
                                "
                                >点击进入</span
                            >
                        </div>
                    </li>

                    <li class="another" @click="anotherFun">
                        <img :src="huanyipi" />
                        <p>换一批</p>
                    </li>
                </ul>
            </div> -->
            <!-- <div class="various-lists">
                <div
                    class="list"
                    v-for="(item,index) in goodsType"
                    :key="index"
                    @click="toProductList(item,1)"
                >
                    <span class="title">{{item.name}}</span>
                    <div class="subtitle">{{item.describe}}</div>
                    <div class="img-list">
                        <img v-for="items in item.goods" :key="items.id" :src="URL + items.pic_url" alt />
                    </div>
                </div>
            </div> -->
            <!-- <div
                class="gongyi same"
                v-for="(item, page) in allFloor"
                :key="page"
               
            >
                <h1>{{ page + 1 }}F {{ item.floor.floor.class_name }}</h1>
                <ul class="l tabul">
                    <li
                        class="l tab "
                        :key="index"
                        v-bind:class="{ active: isactive[page] == index }"
                        @click="tab(index, page)"
                        v-for="(gongyis, index) in gongyi"
                    >
                        {{ gongyis }}
                    </li>
                </ul>
                <div class="huaping l" @click="goToGoodsClass(item.floor, 1)">
                    <img
                    
                        class="l classImg"
                        v-lazy="
                            item.floor.pic_url === ''
                                ? classImg 
                                : URL + item.floor.floor.pic_url    
                        "
                        
                    />
                    <div class="bottom l">
                        <ul class="bottomul">
                            <li
                                class="l bottomli"
                                @click="entryClass(jingruis)"
                                v-for="(jingruis, index) in item.floor.class_two"
                                :key="index"
                            >
                                {{ jingruis.class_name }}
                            </li>
                        </ul>
                    </div>
                </div>
                <div class="swiper l" @mouseover="stop()" @mouseout="move()">
                    <div class="slideshow">
                        <el-carousel
                            :interval="5000"
                            arrow="always"
                            :height="imgHeight"
                        >
                            <el-carousel-item
                                v-for="(img, index) in item.imgArray"
                                :key="index"
                            >
                               
                                <img
                                    :src="URL + img.pic_url
                                    "
                                    @click="goto(img)"
                                />
                            </el-carousel-item>
                        </el-carousel>
                    </div>
                </div>
                <div class="showdiv r" v-if="tabindex == 0">
                    <ul class="showul l">
                        <li
                            :key="index"
                            class="showli l"
                            @click="entryGoods(thing)"
                            v-for="(thing, index) in item.goods"
                        >
                            <img
                                v-if="thing.pic_url"
                                class="hp w_h"
                                :src="URL + thing.pic_url"
                            />
                            <img
                                v-else
                                class="hp w_h"
                                src="../../assets/img/left.jpg"
                            />
                            <p
                                class="jieshao"
                                @mouseenter="updateXY(thing.title, $event)"
                                @mouseleave="updataFullFlag"
                            >
                            <el-dropdown v-if="thing.name">
                                <span class="first-name">{{ thing.name }}</span>
                                <el-dropdown-menu slot="dropdown" v-if="thing.classification">
                                <el-dropdown-item>{{thing.classification}}</el-dropdown-item>
                                </el-dropdown-menu>
                            </el-dropdown>
                            &nbsp;{{ thing.title }}
                            </p>
                            <div class="jiage l">
                                ￥
                                <span>{{ thing.goods_price }}</span>
                                <div
                                    style="margin-left:40px;display:inline-block;font-size:12px;color:#656565"
                                >
                                    已售{{ thing.sales_sum }}件
                                </div>
                            </div>
                        </li>
                    </ul>
                </div>
                <div class="showdiv r" v-else>
                    <ul class="showul l">
                        <li
                            :key="index"
                            class="showli l"
                            @click="entryGoods(thing)"
                            v-for="(thing, index) in item.goods"
                        >
                            <img
                                v-if="thing.pic_url"
                                class="hp w_h"
                                :src="URL + thing.pic_url"
                            />
                            <img
                                v-else
                                class="hp w_h"
                                src="../../assets/img/left.jpg"
                            />
                            <p
                                class="jieshao"
                                @mouseenter="updateXY(thing.title, $event)"
                                @mouseleave="updataFullFlag"
                            >
                            <el-dropdown v-if="thing.name">
                                <span class="first-name">{{ thing.name }}</span>
                                <el-dropdown-menu slot="dropdown" v-if="thing.classification">
                                <el-dropdown-item>{{thing.classification}}</el-dropdown-item>
                                </el-dropdown-menu>
                            </el-dropdown>
                            &nbsp;{{ thing.title }}
                            </p>
                            <div class="jiage l">
                                ￥
                                <span>{{ thing.goods_price }}</span>
                                <div
                                    style="margin-left:40px;display:inline-block;font-size:12px;color:#656565"
                                >
                                    已售{{ thing.sales_sum }}件
                                </div>
                            </div>
                        </li>
                    </ul>
                </div>
               
                <img
                    v-show="!item.button.hasOwnProperty('length')"
                    class="bottomnav"
                    :src="URL + item.button.pic_url"
                    @click="goto(item.button)"
                />
            </div> -->
            <!-- <div class="more">
                <span v-if="floorIndex > 0 && floorIndex <= floorCount" @click="getFloor"
                    >加载更多...</span
                >
                <span v-else class="isEnd"
                    ></span
                >
            </div> -->
            <div class="box-title flash">
                <!-- <div class="title">Flash Sale</div> -->
                <div class="title">限时秒杀</div>
                <a  v-if="seckilData == '' || seckilData == null"></a>
                <a  v-else @click="detailFlashSale" class="shop_more">VIEW MORE</a>
            </div>
            <div class="box-body flash_sale">
                <div class="flash_header">
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
                            <!-- <div class="spr-time l" :class="{ active: itemIndex == index }" v-for="(item, index) in timeData.slice(0,2)" :key="index"> -->
                            <div class="spr-time l" v-for="(item, index) in dateTime" :key="index">
                                <p class="spr-hor">{{item.startTime}}</p>
                                <p class="spr-btn">{{item.name}}</p>
                            </div>
                        </div>
                        <!-- <div class="sale_time r">01:30 Tomorrow</div> -->
                    </div>
                </div>
                <div v-for="(item,index) in seckilData.slice(0,6)" :key="index" class="sale_product" @click="toRushBuy(item.goods_id)">
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
            <div class="Guess-box">
                <!-- <div class="title">Integral Goods</div> -->
                <div class="title">积分兑换</div>
                <a @click="detailIntegralGoods" class="shop_more">VIEW MORE</a>
            </div>
            <div class="Integralg">
                <div v-for="(item,index) in IntegralGoodsNewList.slice(0,6)" :key="index" class="sale_product">
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
            <div class="OS-box">
                <!-- <div class="title">Official Stores</div> -->
                <div class="title">推荐店铺</div>
                <a @click="detailOfficialStore" class="shop_more">VIEW MORE</a>
            </div>
            <div class="OS-body">
                <div v-for="(item,index) in pinpais.slice(0,6)" :key="index" class="sale_product" @click="OSGoods(item.id)">
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
            <div class="Guess-box">
                <!-- <div class="title">New Products</div> -->
                <div class="title">新品上架</div>
                <a @click="detailNewProduct" class="shop_more">VIEW MORE</a>
            </div>
            <div class="Guess-body">
                <div v-for="(item,index) in newProduct.slice(0,6)" :key="index" class="sale_product" @click="entryGoods(item)">
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
            <div class="Guess-box">
                <!-- <div class="title">Hot Sale</div> -->
                <div class="title">热销商品</div>
                <a @click="detailHotSale" class="shop_more">VIEW MORE</a>
            </div>
            <div class="Guess-body">
                <div v-for="(item,index) in guesshot.slice(0,6)" :key="index" class="sale_product" @click="entryGoods(item)">
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
            <div class="Guess-boxx">
                <!-- <div class="titlee">Guess You Like</div> -->
                <div class="titlee">猜你喜欢</div>
                <!-- <a @click="detailGuessYouLike" class="shop_more">VIEW MORE</a> -->
            </div>
            <div class="Guess-bodyy">
                <div v-for="(item,index) in guesslike" :key="index" class="sale_product" @click="entryGoods(item)">
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
                        <span>{{ item.goods_price }}</span>
                        <div
                            style="margin-left:40px;display:inline-block;font-size:12px;color:#656565"
                        >
                            已售{{ item.sales_sum }}件
                        </div>
                    </div>
                </div>
            </div>
            <div class="more">
                <span v-if="floorIndex > 0 && floorIndex <= floorCount" @click="getguessYouLike"
                    >加载更多...</span
                >
                <span v-else class="isEnd"
                    ></span
                >
            </div>
        </div>
        <foot-com></foot-com>
        <back-top></back-top>
        <!--标题完全显示框-->
        <div
            class="full-display-title"
            v-if="fullTitieFlag"
            :style="displayTitleStyle"
        >
            {{ fullTitle }}
        </div>
    </div>
</template>

<script>
import $ from "jquery";
import hp from "@/assets/img/hp.jpg";
import avatar from "@/assets/img/gongniu.jpg";
import avatars from "@/assets/img/hp1.jpg";
import ns from "@/assets/img/ns.png";
import chun from "@/assets/img/chun.png";
import maotai from "@/assets/img/maotai.png";
import huanyipi from "@/assets/img/huanyipi.jpg";
import banner1 from "@/assets/img/banner1.jpg";
import banner2 from "@/assets/img/banner2.jpg";
import banner3 from "@/assets/img/banner3.jpg";
// import Swiper from "swiper";
import { Swiper, SwiperSlide } from 'vue-awesome-swiper';
// import CommonHeader from "../../common/Header";
import "swiper/dist/css/swiper.min.css";

export default {
    name: 'swiper-example-loop-group',
    title: 'Loop mode with multiple slides per group',
    components: {
      Swiper,
      SwiperSlide
    },
    data() {
        return {
            swiperOption: {
            slidesPerView: 5,
            autoplay: 4000,
            slidesPerGroup: 1,
            loop: true,
            prevButton: ".swiper-button-prev",
            nextButton: ".swiper-button-next"
            },
            classImg: hp,
            huanyipi: huanyipi,
            animate: false, //广告滚动处控制
            allFloor: [], //所有楼层的数据
            floorIndex: 1,
            tabindex: 0,
            all: false,
            onoff: "",
            img_url: maotai,
            avatar: avatar,
            fullTitle: "",
            displayTitleStyle: {
                top: "",
                bottom: ""
            },
            nav: [1, 2, 3, 4, 5, 6, 7, 8, 9],
            avatars: avatars,
            display: null,
            showClassify: false,
            marksArr: [], //比对图片索引的变量
            // imgArray: [{ pic_url: ns }, { pic_url: chun }, { pic_url: maotai }],
            isbackcolor: "",
            pinpais: [],
            class_id: 0, //分类ID
            iTem: [],
            condata: [],
            condata2: [],
            condata3: [],
            Array: [],
            gongyi: ["新品推荐", "热卖商品", "猜您喜欢"],
            arr: [],
            items: [],
            isactive: [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0],
            isBottom: false,
            bannerArr: [banner1, banner2, banner3],
            banner: [],
            bannerHeight: "400px",
            imgHeight: "400px",
            userInfo: {},
            panicList: [],
            panicAd: {},
            fullTitieFlag: false,
            Go: 0,
            flag:true,
            fromChild: '',
            isEnd: false,
            floorCount: null,
            imgStoreLogo:'',
            guesslike:[],
            guesshot:'',
            timeData: [],
            itemIndex: 0,
            newProduct:'',
            goodsType: [],
            seckilData:[],
            IntegralGoodsNewList: [],
            showTime: 0,
            countDownTime:0,
            showTimeData: '',
            timeDataFilter: [],
            dateTime: [],
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
        this.pcStoreLogo();
        this.getGoodsType();
        this.getseckillList();
        this.getFavIcon();   
        this.getFootData();
        this.getTimeList();
        this.floorCount = localStorage.getItem('countF')
        // this.getFloor();
        this.GuessFloor();
        this.getguessYouLike();
        this.getGoodsNewList();
        // SH-OP-SN
        // if (
        //     sessionStorage.getItem("userHeaderImg") ||
        //     sessionStorage.getItem("userName")
        // ) {
        //     this.userInfo = {
        //         userName: sessionStorage.getItem("userName"),
        //         userHeaderImg: sessionStorage.getItem("userHeaderImg")
        //     };
        // }
        // if (
        //     localStorage.getItem("userNamee") ||
        //     localStorage.getItem("userHeaderImgg")
        // ) {
        //     this.userInfo = {
        //         userName: localStorage.getItem("userNamee"),
        //         userHeaderImg: localStorage.getItem("userHeaderImgg"),
        //     };
        // }
        if (sessionStorage.getItem('userHeaderImg') || sessionStorage.getItem('userName')) {
            this.userInfo = {
                userName: sessionStorage.getItem('userName'),
                userHeaderImg: sessionStorage.getItem('userHeaderImg')
            }
        };
        this.anotherFun();
    },
    computed: {
        
        
    },
    mounted() {    
        this.getHome();
        this.getPanicIndex();
        setInterval(this.scroll, 3000);
        var that = this;
        $(window).scroll(function() {
            if (!that.$refs.home) return;
            var bot = 700;
            if (
                that.isBottom == false &&
                bot + $(window).scrollTop() >=
                    $(document).height() - $(window).height()
            ) {
                that.isBottom = true;
                if (that.floorIndex >0) {
                    that.floorIndex++;
                    that.getguessYouLike();
                    // that.getFloor();
                }
            }
        });
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
        countDownTimer() {
            // if(this.countDownTime > 999) {
            //     setTimeout(() => {
            //         this.countDownTime -= 1000;
            //         var seconds = Math.floor((this.countDownTime / 1000) % 60);
            //         var minutes = Math.floor((this.countDownTime / (1000 * 60)) % 60);
            //         var hours = Math.floor((this.countDownTime / (1000 * 60 * 60)) % 24);
            //         this.showTime = hours + "时: " + minutes + "分：" + seconds + "秒";
            //         this.countDownTimer()
            //     }, 1000)
            // }
            if(this.countDownTime > 0) {
                setTimeout(() => {
                    this.countDownTime -= 1;
                    var h = Math.floor(this.countDownTime / 3600);
                    var m = Math.floor(this.countDownTime % 3600 / 60);
                    var s = Math.floor(this.countDownTime % 3600 % 60);

                    var hDisplay = h > 0 ? h + (h == 1 ? "时: " : "时: ") : 0 + "时: ";
                    var mDisplay = m > 0 ? m + (m == 1 ? "分：" : "分：") : 0 + "分：";
                    var sDisplay = s > 0 ? s + (s == 1 ? "秒" : "秒") : 0 + "秒";

                    // var hDisplay = h < 10 ?  "0" + h : h;
                    // var mDisplay = m < 10 ?  "0" + m : m;
                    // var sDisplay = s < 10 ?  "0" + s : s;
                    
                    this.showTime = hDisplay + mDisplay + sDisplay;
                    this.countDownTimer()
                }, 1000)
            }
        },
        getTimeList() {
            this.HTTP(this.$httpConfig.timeList, {}, "post")
                .then(res => {
                    this.timeData = res.data.data;
                    var now = Math.round(new Date().getTime() / 1000);
                    this.showTimeData = now;

                    this.timeData.forEach((time, index) => {
                        if(time.countdown) {
                            this.countDownTime = time.countdown
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
                    this.getseckillList();
                })
                .catch(err => {
                    console.log(err);
                });
        },
        getGoodsNewList() {
        this.HTTP(this.$httpConfig.getIntegralGoodsNewList, {}, "post")
            .then(res => {
            this.IntegralGoodsNewList = res.data.data;
            })
            .catch(e => {
            console.log(e);
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
                        let title=sessionStorage.titleKey + '-' +sessionStorage.getItem('updateDescription');
                        this.showScroll.scrollTitle(title);
                })
                .catch(err => {
                    console.log(err);
                });
        },
        detailFlashSale(index) {
            this.itemIndex = index;
            window.open(
                window.location.origin +
                    "/flashSaleDetail"
            );
        },
        detailOfficialStore() {
            window.open(
                window.location.origin +
                    "/officialStoreDetail"
            );
        },
        detailGuessYouLike() {
            window.open(
                window.location.origin +
                    "/guessYouLikeDetail"
            );
        },
        detailHotSale() {
            window.open(
                window.location.origin +
                    "/hotSaleDetail"
            );
        },
        detailNewProduct() {
            window.open(
                window.location.origin +
                    "/newProductDetail"
            );
        },
        detailIntegralGoods() {
            // window.open(
            //     window.location.origin +
            //         "/integralGoodsDetail"
            // );
            window.open(
                window.location.origin +
                    "/integral"
            );
        },
        getseckillList() {
            this.HTTP(this.$httpConfig.seckillList, {}, "post")
                .then(res => {
                    this.seckilData = res.data.data;
                })
                .catch(err => {
                    this.seckilData = res.err.data.data;
                    console.log(err);
                });
        },
        pcStoreLogo() {
            this.HTTP(this.$httpConfig.pcStoreLogo, {}, "post")
                .then(res => {
                   this.imgStoreLogo=res.data.data.pc_store_logo; 
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
        getGoodsType() {
            this.HTTP(this.$httpConfig.goodsType, {}, 'post').then((res) => {
				this.goodsType = res.data.data;
            });
        },
        onChildClick (value) {
            this.fromChild = value
            if(this.fromChild == 'false') {
                location.reload();
            }
        },
        onClick(value) {
            this.floorCount = value
        },
        goToGoodsClass(item, type) {
            window.open(
                window.location.origin +
                    "/goodsClass?class_id=" +
                    item.class_two[0].id +
                    "&id=" +
                    item.floor.id +
                    "&class_name=" +
                    item.floor.class_name +
                    "&type=" +
                    type
            );
        },
        toProductList(item, type) {
            window.open(
                window.location.origin +
                    "/productList?attr_type=" +
                    item.id +
                    "&class_name=" +
                    item.name +
                    "&type=" +
                    type
            );
        },
         
        // authenticated() {
        //     let somethingtemp = localStorage.getItem('loginuserdata')
        //     return somethingtemp;
        // },
        

        //轮播跳转
        goto(img) {
            if (img && img.hasOwnProperty("ad_link")) {
                location.href = img.ad_link;
            }
            return;
        },
        toRushBuy(id) {
            // this.$router.push({
            //     name: "flash_product",
            //     query: {
            //         id: id
            //     }
            // });
            window.open(
                window.location.origin +
                    "/flash_product?id=" + id
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
        getPanicIndex() {
            this.HTTP(this.$httpConfig.panicIndex, {}, "post").then(res => {
                if (res.data.status == 1) {
                    this.panicList = res.data.data.panic;
                    this.panicAd = res.data.data.ad;
                    // setTimeout(() => {
                    //     var mySwiper = new Swiper(".swiper-container", {
                    //         loop: true,
                    //         autoplay: 4000,
                    //         slidesPerView: 5,
                    //         slidesPerGroup: 1,
                    //         prevButton: ".swiper-button-prev",
                    //         nextButton: ".swiper-button-next"
                    //     });
                    // }, 500);
                }
            });
        },
        //头像判断登录跳转
        HeadPortrait() {
            if (this.userInfo.userName != null) {
                this.$router.push({
                    name: "greet"
                });
            } else {
                this.$router.push({
                    name: "passwordLogin"
                });
            }
        },
        toLink(l, n) {
            if (n == 0) {
                window.open(window.location.origin + "/" + l + "?id=1");
            } else {
                window.open(window.location.origin + "/" + l);
            }
        },
        toLink1(url) {
            location.href = url;
        },
        Announcement(id) {
            this.$router.push({
                name: "notice",
                params: {
                    id: id
                }
            });
        },
        anotherFun() {
            this.HTTP(this.$httpConfig.randStore, {}, "post").then(res => {
                this.pinpais = res.data.data;
            });
        },
        entryClass(jingruis) {
            this.Go++;
            window.open(
                window.location.origin +
                    "/goodsClass?class_id=" +
                    jingruis.id +
                    "&id=" +
                    this.Go +
                    "&class_name=" +
                    jingruis.class_name
            );
        },
        entryGoods(goods) {
            window.open(
                window.location.origin + "/shopsn_product?id=" + goods.id
            );
        },
        OSGoods(goods_id) {
            this.$router.push({
                path: "storehome",
                query: {
                    id: goods_id
                }
            });
        },
        // addBuyCar(goods) {

        // },
        stop() {},
        move() {},
        getHome()   {

            //获取公告
            this.HTTP(this.$httpConfig.home, {}, "post")
                .then(res => {
                    this.items = res.data.data.announcement;
                    this.pinpais = res.data.data.store;
                    if (res.data.data.banner.length === 0) {
                        this.banner = this.bannerArr;
                    } else {
                        this.banner = res.data.data.banner;
                    }
                })
                .catch(e => {
                    console.log(e);
                });
        },
        GuessFloor() {
                this.HTTP(
                    this.$httpConfig.homefLoor,
                    {
                        page: 1
                    },
                    "post"
                )
                    .then(res => {
                        this.guesshot = res.data.data.hotGoods;
                        this.newProduct = res.data.data.goods_news;
                    })
                    .catch(e => {
                        console.log(e);
                    });
        },
        // getguessYouLike() {
        //     this.HTTP(
        //         this.$httpConfig.guessYouLike,
        //         {
        //             page: 1
        //         },
        //         "post"
        //     )
        //         .then(res => {
        //           this.guesslike = res.data.data;
        //         })
        //         .catch(e => {
        //             console.log(e);
        //         });
        // },
        async getguessYouLike() {
            if (this.floorIndex > 0 && this.floorIndex <= this.floorCount) {
                await this.HTTP(
                    this.$httpConfig.guessYouLike,
                    {
                        page: this.floorIndex
                    },
                    "post"
                )
                    .then(res => {
                        // this.guesslike.push(res.data.data);
                        if (res.data.status == 1) {
                            if (this.floorIndex == 1) {
                                this.guesslike = res.data.data;
                            } else {
                                this.guesslike = [
                                    ...this.guesslike,
                                    ...res.data.data
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
        /*获取楼层*/
        // async getFloor() {
        //     if (this.floorIndex > 0 && this.floorIndex <= this.floorCount) {
        //         await this.HTTP(
        //             this.$httpConfig.homefLoor,
        //             {
        //                 page: this.floorIndex
        //             },
        //             "post"
        //         )
        //             .then(res => {
        //                 //判断数组是否为空，空则假数据
        //                 // if (res.data.data.middle.length === 0) {
        //                 //     res.data.data.imgArray = this.imgArray;
        //                 // } else {
        //                 //     res.data.data.imgArray = res.data.data.middle;
        //                 // }
        //                  res.data.data.imgArray = res.data.data.middle;
        //                 this.allFloor.push(res.data.data);
        //                 this.marksArr.push(0);
        //                 for (var i = 0; i < this.floorIndex; i++) {
        //                     this.arr[i] =
        //                         this.allFloor[i].goods_news == []
        //                             ? []
        //                             : this.allFloor[i].goods_news;
        //                     this.allFloor[i].goods = this.allFloor[
        //                         i
        //                     ].goods_news;
        //                 }
        //             })
        //             .catch(e => {
        //                 console.log(e);
        //             });

        //         this.isBottom = false;
        //     }
        // },
        scroll() {
            this.animate = true;
            setTimeout(() => {
                this.items.push(this.items[0]);
                this.items.shift();
                this.animate = false;
            }, 500);
        },
        logIn() {
            this.$router.push({
                name: "passwordLogin"
            });
        },
        tab(index, page) {
            let init = index;
            this.isactive[page] = init;
            this.tabindex = index;
            switch (index) {
                case 0:
                    {
                        this.allFloor[page].goods = this.allFloor[
                            page
                        ].goods_news;
                    }
                    break;
                case 1:
                    {
                        this.allFloor[page].goods = this.allFloor[
                            page
                        ].hotGoods;
                    }
                    break;
                // case 2: {
                //   this.arr[page] = this.allFloor[page].rushGoods;
                // }
                //   break;
                case 2:
                    {
                        this.allFloor[page].goods = this.allFloor[
                            page
                        ].loveGoods;
                    }
                    break;
                default:
                    break;
            }
        }
    },
};
</script>

<style lang="less" scoped>

.el-carousel__container {
  position: relative;
  width: 980px;
}

.el-dropdown{
//  width: 230px !important; 
}
.el-dropdown-menu {
    // width: 230px !important;
    position: absolute !important;
    margin: 5px -50px 0 0 !important;
    background-color: #FFF;
    border: 1px solid #de2d35;
    border-radius: 4px;
    -webkit-box-shadow: 0 2px 12px 0 rgba(0,0,0,.1);
    box-shadow: 0 2px 12px 0 rgba(0,0,0,.1);
}
.el-dropdown-menu__item {
  line-height: 10px !important;
  font-size: 12px !important;
  color:rgb(101, 101, 101) !important;
}
  .el-dropdown-menu__item:focus, .el-dropdown-menu__item:not(.is-disabled):hover{
    color:rgb(101, 101, 101) !important;
    background-color: #ffffff !important;
  }

.el-popper .popper__arrow {
  border-width: 6px;
  filter: drop-shadow(0 2px 12px rgba(0,0,0,.03));   
}

.el-popper[x-placement^=bottom] .popper__arrow {
  border-bottom-color: red !important;
}
.el-popper .popper__arrow {
  border-width: 6px;
  filter: drop-shadow(0 2px 12px rgba(0,0,0,.03));
  
}
.el-popper .popper__arrow, .el-popper .popper__arrow:after {
  position: absolute;
  display: block;
  width: 0;
  height: 0;
  border-color: transparent;
  border-style: solid;  
}

</style>

<style type="text/css" scoped>
.full-display-title {
    position: absolute;
    z-index: 222;
    background: #ffffff;
    border: 1px solid #767676;
    padding: 4px;
    color: #575757;
}

.another {
    float: left;
    text-align: center;
    cursor: pointer;
    display: flex;
    align-items: center;
    justify-content: center;
    flex-direction: column;
}

.another img {
    margin-top: 5px !important;
    width: 20px !important;
    height: 20px !important;
}

.swiper-container {
    width: 100%;
}

/* .goods_wrapper .swiper-button-prev {
}

.goods_wrapper .swiper-button-next,
.goods_wrapper .swiper-button-prev {
    background: url(../../assets/img/home_bg.png) no-repeat;
    opacity: 0.5 !important;
    width: 19px;
    height: 29px;
    position: absolute;
    z-index: 2;
    top: 120px;
    cursor: pointer;
}

.goods_wrapper .swiper-button-next {
    background-position: -180px -40px;
    right: 0;
}
.goods_wrapper .swiper-button-prev {
    background-position: -160px -40px;
    left: 0;
} */

.swiper-button-prev, .swiper-container-rtl .swiper-button-next {
    background: url(../../assets/img/home_bg.png) no-repeat;
    opacity: 0.5 !important;
    width: 19px;
    height: 29px;
    position: absolute;
    z-index: 2;
    top: 120px;
    cursor: pointer;
}

.swiper-button-next, .swiper-container-rtl .swiper-button-prev {
    background: url(../../assets/img/home_bg.png) no-repeat;
    opacity: 0.5 !important;
    width: 19px;
    height: 29px;
    position: absolute;
    z-index: 2;
    top: 120px;
    cursor: pointer;
}

.swiper-button-next, .swiper-container-rtl .swiper-button-next {
    background-position: -180px -40px;
    right: 0;
}
.swiper-button-prev, .swiper-container-rtl .swiper-button-next {
    background-position: -160px -40px;
    left: 0;
}
</style>

<style lang="less" scoped>
.wti-sk {
    height: 275px;
    margin-bottom: 30px;

    .sk_inner {
        background: #fff none repeat scroll 0 0;
        overflow: hidden;

        .sk_hd {
            color: #fff;
            float: left;
            height: 275px;
            position: relative;
            width: 190px;
            padding-top: 1.5%;

            img {
                width: 100%;
            }
        }

        .sk_bd {
            float: left;

            .sk_list {
                border-right: 1px solid #f0f0f0;
                float: left;
                height: 275px;
                overflow: hidden;
                position: relative;
                /*width: 999px;*/
                width: 1010px;

                .sk_list_inner {
                    height: 275px;
                    position: relative;
                    /*width: 999px;*/
                    width: 1010px;

                    .sk_item {
                        float: left;
                        height: 275px;
                        position: relative;
                        /*width: 200px;*/
                        width: 202px;

                        .sk_item_lk {
                            display: block;
                            height: 275px;
                            margin: auto;
                            position: relative;
                            text-align: center;
                            width: 198px;
                            color: #555555;
                            .goods-thumb {
                                width: 140px;
                                height: 140px;
                                overflow: hidden;
                                left: 50%;
                                margin-left: -70px;
                                position: absolute;
                                top: 40px;
                                transition: opacity 0.2s ease 0s;
                                img {
                                    max-width: 100%;
                                    transform: scale(1);
                                    transition: all 0.4s ease-in-out 0s;
                                    cursor: pointer;
                                }
                            }
                            .sk_item_name {
                                left: 0;
                                line-height: 30px;
                                overflow: hidden;
                                padding: 0 15px;
                                position: absolute;
                                text-overflow: ellipsis;
                                top: 190px;
                                white-space: nowrap;
                                width: 160px;
                                cursor: pointer;
                            }

                            .sk_item_name:hover {
                                color: #d02629;
                            }

                            .sk_item_price {
                                background: #e6382f none repeat scroll 0 0;
                                height: 20px;
                                left: 15px;
                                line-height: 20px;
                                padding: 1px;
                                position: absolute;
                                top: 228px;
                                width: 160px;

                                .sk_item_price_new {
                                    color: #ffffff;
                                    float: left;
                                    font-size: 14px;
                                    font-weight: bold;
                                    height: 20px;
                                    text-align: center;
                                    width: 80px;
                                }

                                .sk_item_price_origin {
                                    background: #fff none repeat scroll 0 0;
                                    color: #b7bcb8;
                                    float: left;
                                    font-size: 12px;
                                    height: 20px;
                                    text-align: center;
                                    text-decoration: line-through;
                                    width: 80px;
                                }
                            }
                        }
                    }

                    .sk_item:after {
                        background: #f0f0f0 none repeat scroll 0 0;
                        content: "";
                        display: block;
                        height: 275px;
                        position: absolute;
                        right: 0;
                        top: 0;
                        width: 1px;
                    }
                }
            }

            .sk_chn {
                float: left;
                height: 260px;
                position: relative;
                left: 10px;
                top: 8px;
                width: 190px;
                overflow: hidden;
                img {
                    width: 200px;
                    height: 260px;
                }
            }
        }
    }
}

.wrapper {
    width: 1200px;
    // margin: auto;
    // margin-top:90px;
}

.center {
    width: 1200px;
    height: 100%;
    margin: 0 auto;
    display: flex;
    justify-content: center;
    flex-direction: column;
}

.l {
    float: left;
}

.r {
    float: right;
}

.w_h {
    width: 140px;
    height: 140px;
}

#home {
    position: relative;

    .banner {
        width: auto;
        height: 400px;

        .banner-center {
            width: 100%;
            height: 100%;
            position: relative;
            /*background-color: #d02629;*/

            .banner-img {
                // position: absolute;
                top: 0;
                left: 0;
                /*width: auto;*/
                
                width: 980px;
                /*width: 100%;*/
                // margin: 0 auto;
                // height: 400px;
                // width:100%;
                // height:100%;
                img {
                    // width: 100%;
                    // /*height: 100%;*/
                    // height: 98.29%;
                    // z-index: 0;
                    width: 980px;
                    height: 400px;
                }
            }
            #apDiv2 {
                position: absolute;
                pointer-events:none;
                left: 0;
                top: 0;
                width: 1200px;
                // height: 210px;
                z-index: 2;
                // background-color:green;
                opacity: 1;
                margin: 0 auto;
                margin: auto;
                left: 0;
                right: 0;
                bottom: 0;
            }
            
            .denglu {
                pointer-events:auto;
                // position: absolute;
                top: 0;
                z-index: 10;
                margin: auto;
                left: 0;
                right: 0;
                bottom: 0;

                // right: 181px;
                width: 204px;
                height: 390px;
                background: #fbfdfc;
                margin-top: 9px;
                // margin-right: 361px;

                .touxiang {
                    float: left;
                    margin: 10px 0 9px 22px;
                    width: 53px;
                    height: 53px;
                    border-radius: 50%;
                    display: block;
                    cursor: pointer;
                }

                h3 {
                    text-align: center;
                    color: #767676;
                    font-size: 12px;
                    // width: 100%;
                    // padding: 10px 28px 5px 28px;

                    .user-name {
                        display: inline-block;
                        max-width: 170px;
                    }
                }

                .qdl {
                    // margin: 10px 0 0 27px;
                    margin: 10px 30px;

                    .both {
                        float: left;
                        width: 68px;
                        text-align: center;
                        line-height: 22px;
                        // height: 22px;
                        border: 1px solid #323232;
                        font-size: 12px;
                    }

                    .qingdenglu {
                        cursor: pointer;
                        color: #a8a8a8;
                        margin-bottom: 5px;
                    }

                    .kaidian {
                        color: #d02629;
                        border-color: #ac9549;
                        // margin-left: 10px;
                        cursor: pointer;
                    }
                }

                .wufeng {
                    height: 162px;
                    margin: 10px 0 0;
                    overflow: hidden;

                    .item {
                        overflow: hidden;
                    }

                    li {
                        cursor: pointer;
                        width: 165px;
                        float: left;
                        line-height: 25px;
                        font-size: 12px;
                        overflow: hidden;
                        white-space: nowrap;
                        text-overflow: ellipsis;
                        padding-left: 22px;
                    }
                    li:hover {
                        color: red;
                    }
                }

                h4 {
                    width: 100%;
                    height: 24px;
                    line-height: 24px;
                    margin-top: 10px;
                    background: #eeeeee;
                    font-size: 12px;
                    color: #000000;
                    padding-left: 22px;
                }

                .rukou {
                    color: #434343;
                    margin-top: 11px;
                }

                .ul {
                    li {
                        float: left;
                        width: 68px;
                        border-right: 1px solid #eee;
                        height: 59px;
                        text-align: center;

                        img {
                            display: block;
                            margin: 16px auto 6px;
                        }

                        i {
                            margin-top: 14px;
                            margin-bottom: 5px;
                            color: #000;
                            font-size: 18px;
                        }

                        a {
                            color: #8f8f8f;
                            font-size: 12px;
                        }

                        &:hover a {
                            color: red;
                        }
                    }

                    li:last-child {
                        border: 0;
                    }
                }
            }  
        }
    }

    .paimai {
        width: 1200px;
        height: 170px;
        margin-top: 14px;
        display: flex;

        img {
            margin-right: 7px;
        }

        img:last-child {
            margin: 0;
        }
    }
    .box-title{
        padding: 30px 0 0px 0;
        .title {
            font-size: 22px;
            color: #333;
            padding-top: 5px;
            float: left;
            font-weight: 500;
        }
        .shop_more {
            color: #d02629;
            border-color: #d02629;
            width: 110px;
            font-weight: 500;
            font-size: 14px;
            float: right;
            display: block;
            border: 1px solid #d02629;
            border-radius: 2px;
            height: 35px;
            background-color: transparent;
            line-height: 35px;
            text-align: center;
            padding-left: 10px;
            padding-right: 10px;
            // margin-right: 11px;
            // margin-top: 13px;
        }
    }  

    .box-body {
        // height: 375px;
        height: auto;
        .flash_header {
            overflow: hidden;
            border-top: 1px solid #d5d5d5;
            border-bottom: 1px solid #d5d5d5;
            height: 60px;
            line-height: 60px;
            margin-bottom: 10px;
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
                // width: 740px;
                // overflow: scroll;
                .spr-time{
                    margin-left: 30px;
                    display: flex;
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
                        margin: 15px 6px;
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
            }
        }
        .sale_product {
            height: 320px;
            width: 188px;
            float: left;
            margin-right: 14.2px;
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
        .sale_product:last-child{
           margin-right: 0; 
        }
        .sale_product:hover {
            box-shadow: 0 2px 4px 0 rgba(0,0,0,.25);
        } 
    }
    .OS-box{
        padding: 13px 0 0px 0;
        // border-top: 1px solid #d5d5d5;
        border-bottom: 1px solid #d5d5d5;
        margin-bottom: 10px;
        .title {
            font-size: 22px;
            color: #333;
            padding-top: 5px;
            float: left;
            font-weight: 500;
        }
        .shop_more {
            color: #d02629;
            border-color: #d02629;
            width: 110px;
            font-weight: 500;
            font-size: 14px;
            float: right;
            display: block;
            border: 1px solid #d02629;
            border-radius: 2px;
            height: 35px;
            background-color: transparent;
            line-height: 35px;
            text-align: center;
            padding-left: 10px;
            padding-right: 10px;
            // margin-right: 11px;
            // margin-top: 13px;
        }
    } 
    .OS-body {
        height: 300px;
        .sale_product {
            height: 275px;
            width: 188px;
            float: left;
            margin-right: 14.2px;
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
    .Guess-box{
        padding: 13px 0 0px 0;
        // border-top: 1px solid #d5d5d5;
        border-bottom: 1px solid #d5d5d5;
        margin-bottom: 10px;
        .title {
            font-size: 22px;
            color: #333;
            padding-top: 5px;
            float: left;
            font-weight: 500;
        }
        .shop_more {
            color: #d02629;
            border-color: #d02629;
            width: 110px;
            font-weight: 500;
            font-size: 14px;
            float: right;
            display: block;
            border: 1px solid #d02629;
            border-radius: 2px;
            height: 35px;
            background-color: transparent;
            line-height: 35px;
            text-align: center;
            padding-left: 10px;
            padding-right: 10px;
            // margin-right: 11px;
            // margin-top: 13px;
        }
    }
    .Guess-boxx{
        padding: 13px 0 0px 0;
        border-bottom: 1px solid #d5d5d5;
        margin-bottom: 10px;
        .titlee {
            font-size: 22px;
            color: #333;
            padding-top: 5px;
            padding-bottom: 20px;
            float: left;
            // font-weight: lighter;
            font-weight: 500;
        }
    }
    .Guess-body {
        height: 305px;
        .sale_product {
            height: 280px;
            width: 188px;
            float: left;
            margin-right: 14.2px;
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
    .Guess-bodyy {
        height: auto;
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
    .Integralg {
        height: 325px;
        .sale_product {
            height: 300px;
            width: 188px;
            float: left;
            margin-right: 14.2px;
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

    .dianpu {
        width: 100%;
        height: 307px;
        margin-top: 14px;

        .left {
            /*width: 435px;*/
            width: 387px;
            height: 305px;
            img {
                height: 300px;
            }
        }

        ul {
            margin-left: 9px;
            /*width: 752px;*/
            width: 801px;
            border: 0.5px solid #e8e8e8;
            li {
                width: 100px;
                height: 100px;
                border: 0.5px solid #e8e8e8;
                position: relative;

                img {
                    width: 100%;
                    height: 100%;
                }

                .click {
                    display: none;
                    width: 100%;
                    height: 100%;
                    background: rgba(0, 0, 0, 0.6);
                    position: absolute;
                    top: 0;
                    left: 0;
                    cursor: pointer;

                    span {
                        display: block;
                        width: 64px;
                        height: 22px;
                        background: #d02629;
                        line-height: 22px;
                        text-align: center;
                        border-radius: 15px;
                        color: #f4e0bd;
                        font-size: 12px;
                        position: absolute;
                        left: 0;
                        top: 0;
                        bottom: 0;
                        right: 0;
                        margin: auto;
                    }
                }

                &:hover .click {
                    display: block;
                }
            }
        }
    }

    .various-lists {
        padding: 2%;
        display: flex;
        flex-wrap: wrap;
        background: #f1f1f1;
        .list {
            padding-top: 15px;
            border-radius: 10px;
            width: 15.7%;
            padding-bottom: 10px;
            background: #ffffff;
            margin-bottom: 10px;
            margin-left: 10px;
            .title {
                padding: 3px 10px;
                font-size: 15px;
                border: 1px solid orange;
                background: orange;
                margin: 0 10px 10px 10px;
                border-radius: 10px;
                color: #ffffff;   
            }
            .subtitle {
                font-size: 12px;
                padding-left: 12px;
                color: #999999;
                margin-top:10px;
            }
            .img-list {
                display: flex;
                align-items: center;

                img {
                    width: 60px;
                    height: 60px;
                    margin-left: 15px;
                    margin-top: 5px;
                }
            }
        }    
    }
    .same {
        min-height: 550px;
        height: auto;
        margin-top: 15px;

        .same:first-child {
            margin-top: 24px;
        }

        h1 {
            font-size: 20px;
            color: #010101;
            float: left;
            line-height: 35px;
            width: 886px;
            border-bottom: 1px solid #e4393c;
        }

        .tabul {
            border-bottom: 1px solid #e4393c;

            .tab {
                width: 98px;
                height: 35px;
                line-height: 35px;
                text-align: center;
                border: 1px solid #e6e8e7;
                margin-right: 10px;
                border-bottom: 1px;
                background: #fff;
                cursor: pointer;
            }

            .active {
                border-color: #e4393c;
                transform: translateY(1px);
                border-bottom: 0;
                color: #e4393c;
            }

            .tab:last-child {
                margin: auto;
            }
        }

        .huaping {
            height: 488px;
            width: 210px;

            .classImg {
                width: 210px;
                height: 314px;
            }

            .bottom {
                height: 174px;
                width: 100%;
                background: #f6f6f6;

                .bottomul {
                    margin-top: 15px;

                    .bottomli {
                        width: 84px;
                        height: 32px;
                        background: #fff;
                        line-height: 32px;
                        text-align: center;
                        font-size: 12px;
                        margin: 5px 0 0 14px;
                        cursor: pointer;

                        &:hover {
                            color: red;
                        }
                    }
                }
            }
        }

        .swiper {
            width: 379px;
            height: 488px;
            overflow: hidden;
            position: relative;

            .slideshow {
                width: 379px;
                height: 488px;
                cursor: pointer;
            }
        }

        .showul {
            width: 611px;
            // height: 488px;
            padding-left: 1px;
            border-right: 1px solid #e7e8e3;
            border-bottom: 1px solid #e7e8e3;

            .showli {
                width: 203px;
                height: 244px;
                //border-right: 1px solid #e7e8e3;
                //border-bottom: 1px solid #e7e8e3;
                cursor: pointer;

                img {
                    transition: all 300ms linear 300ms;
                    -webkit-transition: all 300ms linear 300ms;
                }

                img:hover {
                    -webkit-transform: scale(1.1); /*1.1放大值*/
                    -moz-transform: scale(1.1);
                    -o-transform: scale(1.1);
                    -ms-transform: scale(1.1);
                }

                .hp {
                    margin: 15px 0 0 31px;
                }

                .jieshao {
                    height: 36px;
                    width: 147px;
                    line-height: 18px;
                    margin: 9px 0 0 31px;
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
                    padding: 0 7px 1px 7px;
                }
                }

                .jiage {
                    color: #f64e4d;
                    font-size: 13px;
                    margin: 12px 0 0 31px;
                }

                .gouwuche {
                    width: 30px;
                    cursor: pointer;
                    height: 30px;
                    background: url(../../assets/img/buyCart.png) no-repeat;
                    background-size: 100% 100%;
                    margin: 4px 28px 0 0;
                }
            }

            .showli:nth-child(3n) {
                border-right: 0;
            }
        }

        .bottomnav {
            width: 100%;
            margin-top: 12px;
            height: 100px;
            cursor: pointer;
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

</style>

<!-- https://stackoverflow.com/questions/14821425/overlapping-divs-click
http://jsfiddle.net/hj9UL/1/ -->
