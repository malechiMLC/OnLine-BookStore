<template>
  <!-- ********************************

        created by：马睿祺 2017302580166

    *********************************** -->
  <a-row style="margin:30px 20px;"
         type="flex"
         justify="start">
    <a-col :xs="24"
           :sm="24"
           :md="24"
           :lg="8"
           :xl="6">
      <a-card :bordered="false"
              style="margin-top:7px;">

        <!-- 封面图片 -->
        <img :src="book.coverUrl"
             object-fit="contain"
             class="img-cover-detail">

        <!-- 右侧正文 -->

      </a-card>
    </a-col>

    <a-col :xs="24"
           :sm="24"
           :md="24"
           :lg="16"
           :xl="18">
      <a-card :bordered="false">
        <a-card-meta align="left">
          <template slot="description">
            <span style="color:black;line-height:35px;font-size:large;font-weight:bold;">{{book.title}}</span><br />

            <p align="left"
               style="letter-spacing: 1px;color:#555555;font-size:1.1vw;line-height:25px;"> {{book.introduction}}
            </p>
            <a-row type="flex"
                   justify="start">
              <a-col :xs="24"
                     :sm="12"
                     :md="12"
                     :lg="12"
                     :xl="12">
                <span style="color:#555555;line-height:20px; font-size:small;">作者：{{book.author}}</span></a-col>
              <a-col :xs="24"
                     :sm="12"
                     :md="12"
                     :lg="12"
                     :xl="12"><span style="color:#555555;line-height:20px; font-size:small;">出版社：{{book.publisher}}</span>
              </a-col>
            </a-row><br />

            <!-- 评分 -->
            <a-rate v-model='stars'
                    disabled />
            <span style="font-size:0.9vw;color:#999999;">{{stars}} 星</span><br />
            <br />

            <a-tag color="cyan"
                   style="margin-top:0.35vw;margin-bottom:10px;">{{book.category}}</a-tag><br />
            <span style="font-size:small;color:#999999;">价格 </span>
            <span style="font-weight:600;color:#ea1;line-height:20px;font-size:large;margin-left:10px;">￥{{book.price}}</span><br />

            <span style="font-size:x-small;color:#999999; line-height:40px;"
                  v-if="discounts.length>0">本店活动 </span>
            <span style="color:#B5621B; margin-left:20px;letter-spacing:1px;"
                  v-for="(item, index) in discounts"
                  :key="index">满{{item.total}}减{{item.discount}}</span><br />
            <span style="font-size:x-small;color:#999999;">销量 </span>
            <span style="font-size:x-small;color:#999999;margin-left:10px;">{{book.sales}}</span><br />
            <span style="font-size:x-small;color:#999999; line-height:40px;">库存 </span>
            <span style="font-size:x-small;color:#999999;margin-left:10px;">{{book.stock}}</span><br />

            <a-button style="background-color:#EAF4EB;margin-top:30px;"
                      @click="addtocart(book.isbn)">加入购物车</a-button>
            <a-button style="margin-left:20px;margin-top:30px;">立即购买</a-button>
            <a-button style="margin-left:20px;margin-top:30px;">收藏</a-button>
          </template>

        </a-card-meta>
      </a-card>
    </a-col>
  </a-row>
</template>

<script>
import '../../../global'
export default {
  props: {
    isbn: {
      type: String
    },
    userId: {
      type: Number,
      default: global.userId
    }
  },
  data () {
    return {
      stars: 0,
      //书籍详细信息,接口：书籍详情
      book: {},
      //所有优惠
      discounts: [],
      flag: false,
      count: 0
    }
  },
  mounted () {
    this.getbookdetail()
  },
  methods: {
    //获取书目详情、星级
    getbookdetail () {
      var _this = this
      _this.axios.get('/api/book/' + _this.isbn)
        .then(function (response) {
          console.log(response.data)
          _this.book = response.data
        })
        .catch(function (error) {
          console.log(error);
        });
      _this.axios.get('/api/comment/book/' + _this.isbn + '/stars', {
      })
        .then(function (response) {
          console.log(_this.isbn)
          _this.stars = response.data
          console.log(_this.stars)
        })
        .catch(function (error) {
          console.log(error);
        });
    },

    //获取满减优惠
    getdiscount () {
      var _this = this
      _this.axios.get('/api/discount')
        .then(function (response) {
          console.log(response.data)
          _this.discounts = response.data.discounts
        }.bind(this))
        .catch(function (error) {
          console.log(error);
        });
    },
    async addtocart (isbn) {
      var _this = this
      await _this.axios.get('/api/cart/' + global.userId)
        .then(async function (response) {
          console.log(response)
          let products = response.data
          for (let i in products) {
            if (products[i].isbn == isbn) {
              _this.count = products[i].count + 1
              _this.flag = true
              break
            }
          }
        })
        .catch(function (error) {
          console.log(error);
        });

      if (_this.flag) {     //修改数量，不重新加入
        _this.axios.put('/api/cart/' + global.userId, {
          isbn: isbn,
          count: _this.count
        })
          .then(function (response) {
            console.log(response)
            if (response.data == true) {
              _this.$message.success('成功加入购物车', 2);
            } else {
              this.$message.error('操作失败', 2);
            }
          })
      } else {          //加入购物车
        console.log('加入购物车：' + global.userId)
        _this.axios.post('/api/cart/' + global.userId, {
          isbn: isbn,
          count: 1
        })
          .then(function (response) {
            console.log(response)
            if (response.data == true) {
              _this.$message.success('成功加入购物车', 2);
            } else {
              this.$message.error('操作失败', 2);
            }
          })
      }

    }
  }
}
</script>

<style scoped>
@import url("../../assets/css/homepage.css");
</style>