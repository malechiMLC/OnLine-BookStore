<template>
  <!-- ********************************

        created by：马睿祺 2017302580166

    *********************************** -->
  <div style="background-color:white;">
    <a-row type="flex"
           justify="space-between"
           align="middle"
           style="margin-left:20px;margin-top:20px;">
      <a-col :xs="24"
             :sm="12"
             :md="12"
             :lg="7"
             :xl="7"
             style="margin-top:10px;">
        <div class="flex-row">
          <a @click="tohome">
            <img src="../../assets/imgs/logo.png"
                 alt="网站图标"
                 class="img-header-logo" /> </a></div>
      </a-col>

      <a-col :xs="24"
             :sm="12"
             :md="12"
             :lg="7"
             :xl="7"
             style="margin-top:10px;">
        <div class="flex-row">
          <!-- 搜索框 -->
          <a-input-search placeholder="输入你想搜索的内容"
                          style="height:3.5vw; "
                          @search="onsearch"
                          :defaultValue="keyword"
                          size="small" />
        </div>
      </a-col>
      <a-col :xs="24"
             :sm="24"
             :md="24"
             :lg="10"
             :xl="10"
             style="margin-top:10px;">
        <a-menu mode="horizontal">
          <a-menu-item key="cart">
            <a @click="tocart"
               target="_blank"
               rel="noopener noreferrer">
              <a-icon type="shopping-cart" />购物车</a>
          </a-menu-item>
          <a-menu-item key="orders">
            <a @click="toorders"
               target="_blank"
               rel="noopener noreferrer">
              <a-icon type="shopping-cart" /> 我的订单</a>
          </a-menu-item>
          <a-menu-item key="me">
            <a @click="tome"
               target="_blank"
               rel="noopener noreferrer">
              <a-icon type="user" />个人主页
            </a>
          </a-menu-item>

        </a-menu>
      </a-col>
    </a-row>
  </div>
</template>

<script>

export default {
  props: {
    keyword: {
      type: String
    },
    userId: {
      type: Number
    }
  },

  methods: {
    // 跳转到主页
    tohome: function () {
      this.$router.push({        name: `Home`, params: {
          userId: this.$route.params.userId
        }      });
    },
    // 点击搜索框，搜索
    onsearch: function (value) {

      if (value != '') {
        if (this.$route.path != '/results') {
          this.$router.push({ name: `SearchResult`, params: { keyword: value } });
        }
        else {                //在SearchResults页面不能再跳转，是同一个route.path
          this.$route.params.keyword = value
          this.$emit('newkeyword', value)
        }
      }
    },
    // 跳转到购物车页面
    tocart: function () {
      this.$router.push({        name: `Cart`,
        params: {
          userId: this.$route.params.userId
        }      });
    },
    //跳转到购物车页面
    tome: function () {
      this.$router.push({        name: `User`,
      });
      console.log(this.userId)

    },
    //跳转到全部订单页面
    toorders: function () {
      this.$router.push({ name: `Order` });
    }
  }
}
</script>

<style scoped>
@import url("../../assets/css/homepage.css");
</style>
