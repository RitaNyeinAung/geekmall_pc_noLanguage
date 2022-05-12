<template>
	<div class="recharge">
		<!-- <common-header></common-header> -->
		<common-header v-on:childToParent="onChildClick"></common-header>
		<div class="logo">
			<router-link to="/home">
				<img :src="URL + logoPhoto" />
			</router-link>
			<p>填写充值金额</p>
		</div>
		<div class="center">
			<ul class="buzhou">
				<li class="l active">1</li>
				<li class="l active">2</li>
				<li class="l">3</li>
			</ul>
			<ul class="zhushi">
				<li class="l actives">填写充值金额</li>
				<li class="l actives">在线支付</li>
				<li class="l">充值完成</li>
			</ul>
      <div class="inline">
        <div class="top">
          <p>充值详情</p>
        </div>
        <div class="jine"><span>*</span>您要充值的金额：<input v-model="recharge_num" @blur="checkPrice" type="text" /></div>
        <p class="huise"><span v-show="isShow">充值金额错误</span></p>
        <div class="fangshi"><span class="spa">*</span><span>请选择支付方式：</span>
          <ul class="l">
            <li v-for="(item,index) in payType" :key="index"><el-radio v-model="radio" :label="item.id">{{item.type_name}}</el-radio></li>
          </ul>
        </div>
		<!-- :before-upload="beforeUploade" -->
		<!-- :show-file-list="uploadImgResult" -->
		<!-- :show-file-list="true" -->
		<div class="imgUpload" v-if="radio == 5"><span class="spa">*</span><span>收据凭证图片: </span>
			<el-upload
				class="avatar-uploader"
				:action="uploadIMGURL"
				:data="uploadToken"
				list-type="picture-card"
				:on-success="uploadSuccess"
				:on-preview="handlePictureCardPreview"
				:on-remove="handleRemove"
				:on-exceed="beyond"
				:limit="1"
				name="voucher"
				accept=".jpg,.jpeg,.png,.gif,.JPG,.JPEG,.GIF,.PNG,"
				style="margin-left: 102px; " 
			>
				<!-- imageUrl -->
				<!-- <img v-if="dialogImageUrl" :src="dialogImageUrl" class="avatar"> -->
				<i class="el-icon-plus"></i>
			</el-upload>
			<el-dialog :visible.sync="dialogVisible">
				<img width="100%" :src="dialogImageUrl" alt="">
			</el-dialog>
		</div>
        <div class="zhifu" @click="toPay">去支付</div>
      </div>
    </div>
		<foot-com></foot-com>
  </div>
</template>
<script>
	export default {
		data() {
			return {
				radio:'',
				payType:[],
				isShow:false,
				logoPhoto:'',
				payMethod:{
					1:'wechatPay',
					2:'aliPay',
					5:'ylPay'
				},
				payData:'',
				recharge_num:'',
				fromChild: '',
				headParams: {
					title: sessionStorage.getItem('titleKey'),
					description: sessionStorage.getItem('updateDescription'),
					keywords: sessionStorage.getItem('contentKey'),
					link:sessionStorage.getItem('pcfavicon')         
				},
				uploadIMGURL: this.$httpConfig.uploadVoucher,
				dialogVisible: false,
				dialogImageUrl: "",
				uploadToken: {
					token: ''
				},
				voucherimg: '',
				imageUrl: '',
				// uploadImgResult: true
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
		this.getPayResult();
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
		this.uploadToken.token = localStorage.getItem("tokenn")
    },
		methods: {
			beforeUploade(file) {
				// this.uploadImgResult = true
			},
			beyond() {
				this.$message.warning("做多五张图片");
			},
			handlePictureCardPreview(file) {
				this.dialogImageUrl = file.url;
				this.dialogVisible = true;
			},
			uploadSuccess(response, file, fileList) {
				// this.uploadImgResult = false
				this.voucherimg = fileList[0].response.pic
				this.imageUrl = URL.createObjectURL(file.raw);
			},
			handleRemove(file, fileList) {
				this.voucherimg = ''
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
						let title=sessionStorage.getItem('titleKey') + '-' +sessionStorage.getItem('updateDescription');
						this.showScroll.scrollTitle(title);
						this.logoPhoto = res.data.data.logo_name;
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
      		wechatPay(){ //微信支付
				sessionStorage.setItem('payData',JSON.stringify(this.payData));
				this.$router.push({
					name: 'wxpay',
					params: {
						total_price: this.recharge_num,
						id: this.radio,
						state:'c'
					}
				})
			},
			aliPay(){ //支付宝支付
				const oDiv = document.createElement('div');
				oDiv.innerHTML = this.payData.data;
				document.body.appendChild(oDiv);
				document.forms.Alipaysubmit.submit();
			},
			ylPay(){
				//银联
			},
			getPayResult() {
				this.HTTP(this.$httpConfig.getPayRechargeResult, {}, 'post').then(res => {
					this.payType = res.data.data;
				}).catch(() => {
					
				})
      		},
      		checkPrice(){
				if(!(/(^[1-9]([0-9]+)?(\.[0-9]{1,2})?$)|(^(0){1}$)|(^[0-9]\.[0-9]([0-9])?$)/.test(this.recharge_num))){
					this.isShow = true;
				}else{
					this.isShow = false;
				}
			},
			toPay(){
				this.checkPrice();
				var that = this;
				if(this.isShow) return false;

				if(this.radio === 5) {
					if(!this.voucher) {
						this.$message.error({
							message: "请上传收据凭证",
							duration: 3000
						})
						return;
					}
					this.HTTP(this.$httpConfig.balanceRecharge, {pay_type_id:this.radio,money:this.recharge_num, voucher: this.voucherimg}, 'post')
					.then((res) => {
						if(res.data.status == 1) {
							this.payData = res.data.data;
							this.$message.success({
								message: "提交成功 等待审核",
								duration: 3000
							})
							this.$router.push({
								path: "/myCharge",
								query: {
									x: 1
								}
							});
						} else {
							this.$message.error({
								message: "提交失败",
								duration: 3000
							})
						}
					}).catch(() => {
						
					})
				} else {
					this.HTTP(this.$httpConfig.balanceRecharge, {pay_type_id:this.radio,money:this.recharge_num}, 'post')
					.then((res) => {
						if(res.data.status == 1) {
							this.payData = res.data.data;
							eval('that.'+ that.payMethod[this.radio]+'()');
						} 
					}).catch(() => {
						
					})
				}
			}
		}
	
	}
</script>
<style>
	.avatar-uploader .el-upload {
		border: 1px dashed #d9d9d9;
		border-radius: 6px;
		cursor: pointer;
		position: relative;
		overflow: hidden;
	}
	.avatar-uploader .el-upload:hover {
		border-color: #409EFF;
	}
	.avatar-uploader-icon {
		font-size: 28px;
		color: #8c939d;
		width: 178px;
		height: 178px;
		line-height: 178px;
		text-align: center;
	}
	.avatar {
		width: 178px;
		height: 178px;
		display: block;
	}
	.el-upload-list--picture-card .el-upload-list__item {
		margin-top: 8px !important;
	}
  .el-radio{
    margin-left: 20px;
  }
  .el-radio__input.is-checked+.el-radio__label {
      color: #333; 
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
	
	.logo {
		height: 115px;
		width: 1200px;
		margin: 0 auto;
		img {
			margin-top: 28px;
			float: left;
			width: 184px;
			height: 53px;
		}
		p {
			font-size: 24px;
			float: left;
			margin: 40px 0 0 29px;
		}
	}
	
	.buzhou {
		height: 50px;
		background: url(../../assets/img/buzhou.jpg) no-repeat center;
		li {
			width: 42px;
			height: 42px;
			border-radius: 50%;
			background: #878788;
			text-align: center;
			line-height: 42px;
			font-size: 20px;
			color: #fff;
			font-weight: 900;
			margin-top: 4px;
		}
		li:nth-of-type(1) {
			margin-left: 207px;
		}
		li:nth-of-type(2) {
			margin: 4px 353px 0 343px;
		}
		.active {
			background: #d02629 !important;
		}
	}
	
	.zhushi {
		height: 42px;
		li {
			font-size: 14px;
			color: #666;
			line-height: 42px;
		}
		li:nth-of-type(1) {
			margin-left: 192px;
		}
		li:nth-of-type(2) {
			margin: 0 335px 0 313px;
		}
		.actives {
			color: #d02629 !important;
		}
	}
	.inline {
			.top {
				overflow: hidden;
				margin: 14px 17px 0px;
				border-bottom: 1px solid #e7e7e7;
				line-height: 37px;
				p {
					width: 104px;
					text-align: center;
					border-bottom: 2px solid #d02629;
					font-size: 14px;
					color: #d02629;
				}
			}
			.jine {
				margin: 29px 0 0 16px;
				font-size: 12px;
				color: #333;
				span{
					color: #f34545;
				}
				input {
					padding-left:5px;
					width: 150px;
					height: 30px;
					border: 1px solid #ccc;
					margin-right: 9px;
				}
			}
			p.huise {
				height: 17px;
				font-size: 12px;
				color: #999;
				margin: 5px 0 18px 123px;
				span{
					color: #f34545;
				}
			}
			.imgUpload {
				overflow: hidden;
				margin: 8px 0 0 16px;
				font-size: 12px;
				color: #333;
				span {
					padding-top: 10px;
					float: left;
				}
				.spa {
					padding-top: 10px;
					color: #f34545;
				}
			}	
			.fangshi {
				overflow: hidden;
				margin: 0px 0 0 16px;
				font-size: 12px;
				color: #333;
				span {
					float: left;
				}
				.spa {
					color: #f34545;
				}
				ul {
					width: 658px;
					min-height: 154px;
					border: 1px solid #ccc;
					li {
						line-height: 50px;
						font-size: 12px;
						color: #333;
						border-bottom: 1px solid #ccc;
						input {
							float: left;
							margin: 19px 8px 0 20px;
						}
					}
					li:last-child {
						border-bottom: 0;
					}
				}
			}
			.zhifu {
				width: 94px;
				height: 32px;
				text-align: center;
				background: #d02629;
				color: #fff;
				line-height: 32px;
				font-size: 12px;
				margin: 30px 0 30px 123px;
				cursor: pointer;
				border-radius: 3px;
			}
		}
</style>