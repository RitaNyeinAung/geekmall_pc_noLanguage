<template>
    <div class="myCard l">
        <div class="top">
            <img :src="URL + receivedData.user_header" alt="" />
            <p>{{ receivedData.user_name }}</p>
            <!-- <span>加入微信关注：</span> -->
        </div>
        <!-- <p class="under"><img src="../../../assets/img/card1.png"/>扫一扫，进入商城</p> -->
        <div id="qrCode_register" ref="qrCodeDiv" class="under">
            扫一扫，进入商城
        </div>
    </div>
</template>

<script>
import QRCode from "qrcodejs2";
export default {
    data() {
        return {
            receivedData: "",
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
        this.receivedData = JSON.parse(sessionStorage.userInfoKey);
        this.getFootData();
        this.getFavIcon(); 
    },
    mounted() {
        this.$nextTick(el => {
            new QRCode(this.$refs.qrCodeDiv, {
                text:
                    "http://m.geekmall.plus/Register?user_id=" +
                    this.receivedData.user_id,
                width: 150,
                height: 150,
                colorDark: "#333333", //二维码颜色
                colorLight: "#fff", //二维码背景色
                correctLevel: QRCode.CorrectLevel.L //容错率，L/M/H
            });
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
    }
};
</script>

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
.myCard {
    width: 980px;
    background: #fff;
    margin: 16px 0;
    padding-bottom: 40px;
    height: 384px;
    .top {
        height: 112px;
        border-bottom: 1px solid #e8e8e8;
        img {
            float: left;
            margin: 10px 14px 0 12px;
            width: 92px;
            height: 92px;
        }
        p {
            font-size: 14px;
            color: #333;
            margin-top: 33px;
            float: left;
            width: 70%;
        }
        span {
            display: inline-block;
            font-size: 14px;
            color: #cd2a2c;
            width: 70%;
        }
    }
    .under {
        width: 136px;
        height: 170px;
        text-align: center;
        font-size: 14px;
        margin: 43px auto 0;
        img {
            float: left;
            margin-bottom: 10px;
        }
    }
}
</style>
