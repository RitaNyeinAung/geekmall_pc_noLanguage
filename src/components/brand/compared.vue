<template>
  <div class="compared">
    <common-header v-on:childToParent="onChildClick"></common-header>
    <!-- 头部 -->
    <head-com></head-com>
		<div class="center">
			<div class="top">
				<el-breadcrumb separator-class="el-icon-arrow-right">
					<el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
					<el-breadcrumb-item :to="{ path: '/brand' }">品牌</el-breadcrumb-item>
					<el-breadcrumb-item>对比</el-breadcrumb-item>
				</el-breadcrumb>
			</div>
		</div>
    <div class="view">
      <el-table
        :data="data"
        border>
        <el-table-column
          prop="brand_name"
          label="品牌"
          width="100">
        </el-table-column>
        <el-table-column
          prop="shop_name"
          label="店铺名称"
          width="150">
        </el-table-column>
        <el-table-column
          prop="title"
          label="标题"
          width="150">
        </el-table-column>
        <el-table-column
          prop="one_name"
          label="一级分类"
          width="150">
        </el-table-column>
        <el-table-column
          prop="two_name"
          label="二级分类"
          width="150">
        </el-table-column>
        <el-table-column
          prop="three_name"
          label="三级分类"
          width="150">
        </el-table-column>
        <el-table-column
          prop="price_market"
          label="原价"
          width="150">
        </el-table-column>
        <el-table-column
          prop="price_member"
          label="现价"
          width="150">
        </el-table-column>
      </el-table>
    </div>
    <!-- 底部 -->
    <foot-com></foot-com>
  </div>
</template>
<script>
export default {
  name: 'compared',
  data () {
    return {
      data: [],
      tableData: [{
        date: '2016-05-02',
        name: '王小虎',
        address: '上海市普陀区金沙江路 1518 弄'
      }, {
        date: '2016-05-04',
        name: '王小虎',
        address: '上海市普陀区金沙江路 1517 弄'
      }, {
        date: '2016-05-01',
        name: '王小虎',
        address: '上海市普陀区金沙江路 1519 弄'
      }, {
        date: '2016-05-03',
        name: '王小虎',
        address: '上海市普陀区金沙江路 1516 弄'
      }],
      fromChild: '',
      headParams: {
          title: sessionStorage.getItem('titleKey'),
          description: sessionStorage.getItem('updateDescription'),
          keywords: sessionStorage.getItem('contentKey'),
          link:sessionStorage.getItem('pcfavicon')         
      }
    }
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
  created () {
    this.getFootData();
    this.getFavIcon();
    this.HTTP(this.$httpConfig.contrastResult, {
      contrast_id: this.$route.params.id
    }, 'post').then(res => {
      this.data = res.data.data
    })
  },
  mounted(){
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
  }
}
</script>
<style>
.view {
  display: flex;
  margin: 50px;
  justify-content: center;
}
</style>
<style lang="less" scoped>
.top{
  	margin: 15px 0;
}
</style>
