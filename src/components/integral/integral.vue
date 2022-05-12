<template>
  <div class="integral">
    <common-header v-on:childToParent="onChildClick"></common-header>
    <head-com v-if="tempid" :navid = tempid></head-com>
    <head-com v-else :navid = 58></head-com>
    <div class="banner">
      <el-carousel :interval="5000" arrow="hover" :height="bannerHeight">
        <el-carousel-item
          v-for="(item, index) in userInfo.balance" :key="index" >
          <a :href="item.ad_link" style="pointer-events:auto; ">
				<img :src="URL + item.pic_url" />
				</a>
        </el-carousel-item>
      </el-carousel>
      <div class="user-info">
        <div class="l denglu">
          <p class="touxiang l">
            <img v-if="userInfo.user" :src="URL + userInfo.user.user_header" />
            <img v-else src="../../assets/img/default-head.png" />
          </p>
          <p class="name l" v-if="userInfo.user">
            Dear：{{ userInfo.user.user_name }}
          </p>
           <p class="name l" v-else></p>
          <p class="l">欢迎回来</p>
          <p class="l"><img src="../../assets/img/qian.png" />积分</p>
          <p class="l" v-if="userInfo.user">
            {{
              userInfo.user.integral === null
                ? "暂无数据"
                : userInfo.user.integral
            }}
          </p>
          <p class="l" v-else></p>
          <p class="l">
            <!-- <span class="l left" @click="toIntegralList(userInfo.user.integral)"> -->
              <span class="l left" @click="toIntegralPoint">
              <img src="../../assets/img/dian.png" />我能兑换
            </span>
            <span class="r right" @click="toGuide">
              <img src="../../assets/img/dian.png" />兑换须知
            </span>
          </p>
        </div>
      </div>
    </div>
    <div class="center">
      <!-- <div class="duihuan">
        <p>
          本周
          <span>热兑商品</span>
        </p>
        <p>
          <span>
            <img src="../../assets/img/upyinhao.png" />购商品返积分，聚惠享不停
            <img src="../../assets/img/downyinhao.png" />
          </span>
        </p>
      </div> -->
      <!-- <div v-if="hotExchangeData.left" class="cargo">
        <span>{{ hotExchangeData === null ? "暂无数据" : "" }}</span>
        <a :href="hotExchangeData.left.ad_link" style="pointer-events:auto; ">
        <img class="l left-image" :src="URL + hotExchangeData.left.pic_url" />
        </a>
        <ul class="l">
          <li
            class="l"
            v-for="(item, index) in hotExchangeData.right"
            :key="index"
          >
            <div @click="goIntegralText(item)" class="li_item">
              <a :href="item.ad_link" style="pointer-events:auto; ">
              <img class="right-goods-img" :src="URL + item.pic_url" />
              </a>
            </div>
            <div
              class="full-display-title"
              v-if="fullTitieFlag"
              :style="displayTitleStyle"
            >
              {{ fullTitle }}
            </div>
          </li>
        </ul>
      </div> -->
      <div class="duihuan">
        <p><span>最新</span>兑换商品</p>
        <p>
          <span>
            <img src="../../assets/img/upyinhao.png" />购商品返积分，聚惠享不停
            <img src="../../assets/img/downyinhao.png" />
          </span>
        </p>
      </div>
      <!-- <div class="more">
        <router-link to="integrallist">查看更多 ></router-link>
      </div> -->
      <div class="shangpin">
        <span>{{ IntegralGoodsNewList === null ? "暂无数据" : "" }}</span>
        <ul>
          <li
            class="l"
            v-for="(item, index) in IntegralGoodsNewList"
            :key="index"
          >
            <img
              :src="URL + item.pic_url"
              :style="
                item.pic_url === null
                  ? 'background: gray'
                  : 'background: transparent;'
              "
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
            />
            <p
              class="jieshao"
              @mouseenter="handleEnter(item.title, $event)"
              @mouseleave="handleLeave(item.title)"
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
          </li>
        </ul>
      </div>
      <div class="view_more">
        <button class="more" @click="toAll">View More</button>
      </div>
    </div>
    <foot-com></foot-com>
  </div>
</template>

<script>
export default {
  data() {
    return {
      bannerHeight: "342px",
      arr: [],
      li: [],
      // hotExchangeData: [],
      IntegralGoodsNewList: [],
      userInfo: [],
      fullTitieFlag: false,
      displayTitleStyle: {
        top: "",
        bottom: ""
      },
      fullTitle:'',
      tempid: '',
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
      this.getFootData();
      this.getFavIcon(); 
  },
  mounted() {
    this.tempid = this.$route.query.id
    //获取用户信息
    this.getIntegralUseInfo();
    //热兑商品
    // this.getAttenStore();
    //最新兑换商品
    this.getGoodsNewList();
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
    handleEnter(title, e) {
      this.fullTitle = title;
      this.displayTitleStyle.top = e.pageY + 30 + "px";
      this.displayTitleStyle.left = e.pageX + 20 + "px";
      this.fullTitieFlag = true;
    },
    handleLeave(title) {
      this.fullTitieFlag = false;
    },
    goIntegralText(item) {
      // this.$router.push({
      //   name: 'IntegralInside',
      //   // query: {
      //   //   goods_id: item.id,
      //   //   id: item.integral_id,
      //   //   integral: item.integral
      //   // }
      // })

    },
    // toIntegralList(integralPoint) {
    //   this.$router.push({
    //         path: "/integrallist",
    //         query: {
    //           integral: integralPoint
    //         }
    //     });
    // },
    toIntegralPoint() {
      this.$router.push({
            path: "/myIntegral"
        });
    },
    toGuide() {
      this.$router.push({
            path: "/guide"
        });
    },
    toAll() {
        this.$router.push({
            path: "/integrallist"
        });
    },
    //获取用户信息
    getIntegralUseInfo() {
      this.HTTP(this.$httpConfig.getIntegralUseInfo, {}, "post", false)
        .then(res => {
          this.userInfo = res.data.data;
        })
        .catch(e => {
          console.log(e);
        });
    },
    lg(a) {
      console.log(a);
    },
    //热兑商品
    // getAttenStore() {
    //   this.HTTP(this.$httpConfig.getHotExchange, {}, "post")
    //     .then(res => {
    //       if (res.data.message === "用户未登录!") {
    //         this.$confirm("用户未登录", "提示", {
    //           confirmButtonText: "确定",
    //           type: "warning",
    //           closeOnClickModal: false,
    //           lockScroll: false,
    //           center: true
    //         })
    //           .then(() => {
    //             window.open(window.location.origin + "/passwordLogin");
    //           })
    //           .catch(err => {
    //             console.error(err);
    //           });
    //       } else {
    //         this.$nextTick(el => {
    //           this.hotExchangeData = res.data.data;
    //         });
    //       }
    //     })
    //     .catch(err => {
    //       console.error(err, "error");
    //     });
    // },
    //最新兑换商品
    getGoodsNewList() {
      this.HTTP(this.$httpConfig.getIntegralGoodsNewList, {}, "post")
        .then(res => {
          this.IntegralGoodsNewList = res.data.data;
        })
        .catch(e => {
          console.log(e);
        });
    }
  }
};
</script>
<style lang="less" scoped>
 .full-display-title{
    position: absolute;
    z-index: 222;
    background: #ffffff;
    border: 1px solid #767676;
    padding: 4px;
    color: #575757;

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
  // margin-top: 175px;
}

.banner {
  height: 342px;
  position: relative;
  .el-carousel {
    width: 100%;
    position: absolute;
    top: 0;
    left: 0;
    img {
      width: 100%;
      height: 100%;
    }
  }
  .user-info {
    // width: 1200px;
    // height: 342px;
    width: 210px;
    height: 342px;
    margin: 0 auto;
    position: relative;
    display: block;
    z-index: 2;
    right: 495px;
    // padding-top: 10px;

    .denglu {
      // width: 210px;
      width: 245px;
      // height: 100%;
      height:342px;
      background: #d02629;
      p {
        width: 100%;
        text-align: center;
      }
      .touxiang {
        width: 108px;
        height: 108px;
        background: #eec43c;
        border-radius: 50%;
        margin: 23px 69px 0;
        img {
          margin: 5px auto;
          display: block;
          border-radius: 50%;
          width: 98px;
          height: 98px;
        }
      }
      .name {
        font-size: 14px;
        color: #fff;
        margin-top: 23px;
      }
      p:nth-of-type(3) {
        font-size: 12px;
        color: #fae2d5;
      }
      p:nth-of-type(4) {
        font-size: 12px;
        color: #fff;
        margin-top: 17px;
        img {
          margin-right: 6px;
        }
      }
      p:nth-of-type(5) {
        font-size: 22px;
        color: #faff7f;
        margin-top: 11px;
      }
      p:nth-of-type(6) {
        margin-top: 37px;
        .left {
          margin-left: 16px;
          font-size: 14px;
          color: #fcf6e9;
          cursor: pointer;
          img {
            margin-right: 15px;
          }
        }
        .right {
          margin-right: 16px;
          font-size: 14px;
          color: #fcf6e9;
          cursor: pointer;
          img {
            margin-right: 15px;
          }
        }
      }
    }
  }
}

.duihuan {
  height: 93px;
  border: 2px dashed #f4f4f4;
  margin: 20px 0;
  p:nth-of-type(1) {
    width: 100%;
    float: left;
    text-align: center;
    font-size: 24px;
    color: #333;
    font-weight: 700;
    margin-top: 15px;
    span {
      color: #ff6000;
    }
  }
  p:nth-of-type(2) {
    width: 100%;
    float: left;
    text-align: center;
    margin-top: 6px;
    span {
      font-size: 12px;
      color: #666;
      img:nth-of-type(1) {
        padding-right: 55px;
      }
      img:nth-of-type(2) {
        padding-left: 55px;
      }
    }
  }
}

.cargo {
  height: 775.5px;
  border: 1px solid #f4f4f4;
  margin-bottom: 10px;
  .left-image {
    width: 320px;
    height: 517px;
    padding: 10px;
  }
  ul {
    width: 878px;
    height: 775.5px;
    li {
      width: 219.5px;
      height: 258px;
      padding: 8px 5px 4px 5px;
      border-right: 1px solid #f4f4f4;
      border-bottom: 1px solid #f4f4f4;
      .desc {
        overflow: hidden;
        text-overflow: ellipsis;
        display: -webkit-box;
        width: 188px;
        padding: 14px 10px;
        text-align: left;
        -webkit-line-clamp: 2;
        -webkit-box-orient: vertical;
        font-size: 12px;
        &:hover{
            cursor: pointer;
        }
      }
      .desc:hover {
        color: red;
      }
      .add_goods {
        display: flex;
        padding: 3px 13px;
        font-size: 12px;
        align-items: center;
        justify-content: space-between;
        .left {
          display: flex;
          flex-direction: column;
          align-items: center;
          .price {
            color: #b4b4b4;
            span {
              text-decoration: line-through;
            }
          }
          .integral {
            color: #e64242;
            span {
            }
          }
        }
        .right {
          .hot_exchange {
            text-align: center;
            background: #de414c;
            height: 20px;
            width: 60px;
            line-height: 20px;
            color: #fff;
          }
        }
      }
      p {
        width: 100%;
        text-align: center;
        cursor: pointer;
      }
      p:nth-of-type(1) {
        font-size: 16px;
        color: #333;
        font-weight: 600;
        margin-top: 15px;
        overflow: hidden;
        text-overflow: ellipsis;
        white-space: nowrap;
        &:hover {
          color: red;
        }
      }
      p:nth-of-type(2) {
        font-size: 12px;
        color: #666;
        margin: 5px 0;
        &:hover {
          color: deepskyblue;
        }
      }
      img.right-goods-img {
        margin: 0 auto;
        display: block;
        height:240px;
        // width: 145px;
        // height: 180px;
      }
    }
    li:nth-child(4n) {
      border-right: 0;
    }
    li:nth-of-type(5) {
      border-bottom: 0;
    }
    li:nth-of-type(6) {
      border-bottom: 0;
    }
    li:nth-of-type(7) {
      border-bottom: 0;
    }
  }
}

.more {
  overflow: hidden;
  margin-bottom: 17px;
  a {
    width: 92px;
    height: 25px;
    background: #b4b4b4;
    font-size: 12px;
    color: #fff;
    text-align: center;
    line-height: 25px;
    float: right;
    display: block;
  }
}

.shangpin {
  overflow: hidden;
  height: auto;
  padding: 0 0 20px 0;
  ul {
    width: 1200px;
    height: auto;
    min-height: 354px;
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    box-sizing: border-box;
    justify-content: flex-start;
    li {
      box-sizing: border-box;
      width: 230px;
      height: 354px;
      border: 1px solid #f4f4f4;
      margin: 5px;
      img {
        display: block;
        margin: 10px;
        width: 210px;
        height: 210px;
      }
      .jieshao {
        font-size: 12px;
        color: #484848;
        margin: 0 11px;
        line-height: 20px;
        cursor: pointer;
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
      }
      .bottom {
        width: 218px;
        height: 54px;
        margin: 0 1px;
        margin-top: 18px;
        p:nth-of-type(1) {
          font-size: 12px;
          color: #b4b4b4;
          margin-top: 13px;
          margin-left: 13px;
          span {
            text-decoration: line-through;
          }
        }
        p:nth-of-type(2) {
          margin-top: 12px;
          margin-right: 9px;
          width: 68px;
          height: 30px;
          background: #de414c;
          text-align: center;
          line-height: 30px;
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
          margin-left: 13px;
          display: -webkit-box;
          overflow: hidden;
          white-space: normal !important;
          text-overflow: ellipsis;
          word-wrap: break-word;
          -webkit-line-clamp: 1;
          -webkit-box-orient: vertical;
        }
      }
      &:hover {
        box-shadow: 2px 4px 6px #f4f4f4;
      }
    }
    li:nth-child(5n) {
      margin-right: 0;
    }
  }
}
.view_more {
    width: 100%;
    padding: 15px 0 25px 0;
    text-align: center;
    .more {
        font-size: 16px;
        font-weight: 700;
        color: #fff;
        background-color: #d02629;
        padding: 10px 100px;
        border-radius: 20px;
        cursor: pointer;
    }
}
</style>
