<template>
	<div class="myintegral l">
		<div class="top">
			<div class="l">
				<ul>
					<li @click="firstTab(0)" :class="{color:isColor==0}">
						<p>积分规则</p>
					</li>
					<li @click="firstTab(1)" :class="{color:isColor==1}">
						<p>我的积分</p>
					</li>
				</ul>
			</div>
			<div class="r">
				<p>我的积分：<span>{{myIntegral.current_integral || 0}}</span></p>
				<p>会员等级：<span>{{myIntegral.current_level_name}}</span></p>
				<p>再积累<span>{{myIntegral.next_integral}}</span>积分</p>
				<p>可升级{{myIntegral.next_level_name}}</p>
			</div>
		</div>
		<div class="under" v-show="isColor==0">
			<div class="thead">
				<p>积分介绍</p>
			</div>
			<ul>
				<li>1、积分是商城会员通过购物获得</li>
				<li>2、累积的积分总额决定会员等级，积分越高，会员等级越高。会员积分 ≈ 累计购物金额</li>
				<li>3、积分值：购物获得的积分是根据确认收货时间计算，积分按照交易金额的个位数取整计算：例如会员的订单是88.0元，则确认收货后并且无退款退货即可得到88积分</li>
				<li>4、确认收货后退款退货，会扣减积分，扣除的积分与当时获得的积分相同</li>
			</ul>
			<div class="thead">
				<p>升级条件</p>
			</div>
			<table>
				<tr>
					<th class="dengji">会员等级</th>
					<th class="jifen">积分</th>
					<th class="tubiao">等级图标</th>
				</tr>
				<tr v-if="ruleInfo" v-for="(info,index) in ruleInfo" :key="info.id">
					<td>{{info.level_name}}</td>
					<td>{{info.integral_small}}--{{info.integral_big}}</td>
					<td><img :src="rankIcons[index]" /></td>
				</tr>
			</table>
		</div>
		<div class="unders" v-show="isColor==1">
			<div class="up">
				<p>来源/用途</p>
				<!-- <p>订单号/退款编号</p> -->
				<p>实付款</p>
				<p>积分</p>
				<p>获取时间</p>
			</div>
      
      <!-- <div class="block" style="padding-top: 20px;">
        <el-date-picker
          v-model="value"
          type="month"
          placeholder="Pick a month">
        </el-date-picker>
      </div> -->
      <div class="thead">
          <span
              class="l"
              @click="open(0)"
              :class="{ bor: isbor == 0 }"
              >积分获取</span
          >
          <span
              class="l"
              @click="open(1)"
              :class="{ bor: isbor == 1 }"
              >积分支出</span
          >
      </div>
      <div class="monthPicker">
        <month-picker-input @change="selectInput" lang="zh" :default-month="currentMonth+1" :default-year="currentYear"></month-picker-input>
      </div>
      <div class="intergral_box" >
        <div class="down">
          <p>当前积分</p>
          <p>订单编号</p>
          <p>变动积分</p>
          <p>交易时间</p>
          <p>备注</p>
        </div>
        <div class="down" v-for="(item, i) in integralLog" :key="i">
          <p>{{item.integral}}</p>
          <p>{{item.order_id}}</p>
          <p v-if="item.changes_integral > 0">
            <span class="increaseCol">{{item.changes_integral}}</span>
            </p>
          <p v-if="item.changes_integral < 0">
            <span class="idecreaseCol">{{item.changes_integral}}</span>
          </p>
          <p>{{item.trading_time}}</p>
          <p>{{item.remarks}}</p>
        </div>
      </div>

      <div class="box2" style="width: 140px;margin: 0 auto;margin-top: 20px">
        <el-pagination @current-change="handleCurrentChange" background layout="total,prev, pager, next,jumper"
                       :current-page.sync="currentPage" :page-size="page_size" :total="page">
        </el-pagination>
      </div>
		</div>
	</div>
</template>

<script>
import { MonthPickerInput } from 'vue-month-picker'
export default {
  data() {
    return {
      ruleInfo: [],
      myIntegral: {},
      levelList: [],
      rankIcons: [
        require("@/assets/img/lv1.png"),
        require("@/assets/img/lv2.png"),
        require("@/assets/img/lv3.png"),
        require("@/assets/img/lv4.png")
      ],
      integralType: {
         0: '-',
         1: '+'
      },
      //我的积分
      isColor: 0,
      integralLog: [],
      currentPage:1,
      page_size:0,
      page:0,
      value: "",
      monthSelect: "",
      currentMonth: "",
      currentYear: "",
      typeData: "",
      isbor: "",
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
    this.getRule();
    this.getMyIntegral();
    this.getFootData();
    this.getFavIcon(); 
    this.currentMonth = new Date().getMonth();
    this.currentYear = new Date().getFullYear();
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
    firstTab(index) {
      if (index == 0) {
            this.getRule();
        } else {
          this.typeData = 1;
            this.Jifen();
        }
        this.isColor = index;
    },
    getRule() {
      this.HTTP(this.$httpConfig.integralRule, {}, "post").then(res => {
        this.ruleInfo = res.data.data;
      });
    },
    handleCurrentChange(currentPage) { //下一页
      this.currentPage = currentPage;
      this.Jifen();
    },
    getMyIntegral() {
      this.HTTP(this.$httpConfig.myIntegral, {}, "post").then(res => {
        this.myIntegral = res.data.data;
      });
    },
    selectInput(data) {
      this.monthSelect = data.selectedYear + "-" + data.monthIndex;
      this.Jifen();
    },
    open(type) {
        if (type == 0) {
            this.typeData = 1;
            this.Jifen();
        } else {
            this.typeData = 0;
            this.Jifen();
        }
        this.isbor = type;
    },
    Jifen() {
      this.HTTP(this.$httpConfig.integralLog, {
        page: this.currentPage,
        date: this.monthSelect,
        type: this.typeData
        }, "post").then((res) => {
        this.integralLog = res.data.data.records;
        this.page = Number(res.data.data.count);
        this.page_size = res.data.data.page_size;

      }).catch(err => {
        console.log(err);
      })
    }
  },
  components: {
		MonthPickerInput
	}
};
</script>

<style>
.month-picker__container {
  position: relative !important;
  top: 10px !important;
}
.month-picker-input {
    padding: 7px 10px !important;
    font-size: 0.85em;
    border-radius: 5px;
    outline: none;
    border: 1px solid rgba(0, 0, 0, 0.15);
    -webkit-transition: all 350ms cubic-bezier(0.165, 0.84, 0.44, 1);
    transition: all 350ms cubic-bezier(0.165, 0.84, 0.44, 1);
    width: 100px;
}
</style>

<style lang="less" scoped>
.l {
  float: left;
}

.r {
  float: right;
}
.down {
  border: 1px solid #f5f5f5;
  overflow: hidden;
  height: 55px;
  // padding: 0px 10px;
  display: flex;
  .increaseCol {
    background: #67a167; 
    color: #fff; 
    padding: 5px 10px; 
    border-radius: 15px;
  }
  .decreaseCol {
    background: #dc5d5f; 
    color: #fff; 
    padding: 5px 10px; 
    border-radius: 15px;
  }
}
.intergral_box {
  border: 2px solid #f5f5f5;
  margin-top: 20px;
  box-sizing: border-box;
  p {
    height: 100%;
    display: flex;
    align-items: center;
    justify-content: center;
    border-right: 2px solid #f5f5f5;
  }
  p:nth-of-type(1) {
    width: 270px;
  }
  p:nth-of-type(2) {
    width: 150px;
  }
  p:nth-of-type(3) {
    width: 150px;
  }
  p:nth-of-type(4) {
    width: 200px;
  }
  p:nth-of-type(5) {
    width: 171px;
  }
}
.intergral_box div:first-child {
  border-top: 0px solid transparent;
}
.intergral_box div:last-child {
  border-bottom: 0px solid transparent;
}
.center {
  width: 1200px;
  margin: 0 auto;
  height: 100%;
}

.myintegral {
  height: 1190px;
  width: 980px;
  background: #fff;
  margin: 16px 0;
  .top {
    overflow: hidden;
    margin: 14px 17px 0px;
    border-bottom: 1px solid #e7e7e7;
    line-height: 37px;
    div.l {
      ul {
        li {
          cursor: pointer;
          float: left;
          width: 104px;
          height: 37px;
          color: #333;
          p {
            margin-top: 10px;
            font-size: 14px;
            text-align: center;
            line-height: 16px;
            border-right: 1px solid #e8e8e8;
          }
        }
        .color {
          border-bottom: 2px solid #d02629;
          color: #d02629 !important;
        }
      }
    }
    div.r {
      p {
        float: left;
        font-size: 14px;
        color: #333;
        margin-left: 21px;
        span {
          color: #d02629;
        }
      }
    }
  }
  .under {
    margin: 0 17px;
    .thead {
      margin-top: 17px;
      height: 26px;
      border-bottom: 1px solid #fdb9af;
      p {
        width: 136px;
        line-height: 26px;
        background: url(../../../assets/img/jifen.png) no-repeat center;
        padding-left: 35px;
        color: #fff;
        font-size: 14px;
      }
    }
    ul {
      border: 1px solid #fdb9af;
      padding: 20px 22px;
      margin: 9px 0 20px 0;
      li {
        line-height: 24px;
        font-size: 12px;
        color: #666;
      }
    }
    table {
      margin-top: 10px;
      border: 1px solid #fdb9af;
      border-bottom: 0;
      th {
        width: 278px;
        text-align: center;
        line-height: 44px;
        background: #fbdbd6;
        font-size: 14px;
        font-weight: 500;
        border-right: 1px solid #f4d3d3;
      }
    }
    .jifen {
      width: 359px;
    }
    .tubiao {
      width: 305px;
      border-right: 0;
    }
    td {
      line-height: 38px;
      border-bottom: 1px solid #fbdbd6;
      text-align: center;
      border-right: 1px solid #f4d3d3;
      font-size: 12px;
      color: #666;
    }
    .up {
      overflow: hidden;
      line-height: 44px;
      background: #f5f5f5;
      border: #e8e8e8;
      margin-top: 20px;
      p {
        float: left;
      }
    }
  }
  .unders {
    margin: 0 17px;
    .up {
      overflow: hidden;
      line-height: 44px;
      background: #f5f5f5;
      border: #e8e8e8;
      margin-top: 20px;
    }
    p {
      float: left;
      text-align: center;
      font-size: 12px;
      color: #333;
    }
    .thead {
        height: 43px;
        line-height: 42px;
        // border-bottom: 1px solid #e8e8e8;
        span.l {
            width: 84px;
            text-align: center;
            font-size: 14px;
            color: #666;
            cursor: pointer;
        }
        .bor {
            border-bottom: 1px solid #d02629;
            color: #d02629 !important;
        }
    }
    .monthPicker {
        padding-top: 20px;
    }
  }
}
</style>
