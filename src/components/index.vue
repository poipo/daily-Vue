<template>
<div>
    <div class="nav">
        <i class="icon-reorder icon-large nav-icon"></i>
        <span class="nav-title">首页</span>
        <i class="nav-more"></i>
    </div>
    <div class="swipe">
        <mt-swipe :auto="2000">
          <mt-swipe-item><img src="https://pic2.zhimg.com/v2-9c100252abe543914ba52a81cc2d9791.jpg" alt="" class="swipe-img"></mt-swipe-item>
          <mt-swipe-item><img src="https://pic1.zhimg.com/v2-ffedd43aaa253d845b19846828927ec8.jpg" alt="" class="swipe-img"></mt-swipe-item>
        </mt-swipe>
    </div>


</div>


</template>

<script>
import { Swipe, SwipeItem } from 'mint-ui';
import 'mint-ui/lib/swipe/style.css';
import Vue from 'vue';
Vue.component(Swipe.name, Swipe);
Vue.component(SwipeItem.name, SwipeItem);


export default {
  name: 'index',
  data () {
    return {
        date: '',
        swipe: [],
        list: []
    }
  },
  beforeMount: function(){
    var self = this
    self.$http.get('/api/4/news/latest').then((res) => {
        self.date = res.body.date;
        self.swipe = res.body.top_stories;
        self.list = res.body.stories;
    });
  }
}
</script>

<style lang="scss">
$main-color: #009dd7;
.swipe{
    height: 4rem;
}
.swipe-img{
    width: 100%;
    height: 100%;
}
.nav-icon{color: #fff; margin-left: .24rem;}
.nav{width: 100%; height: 1rem; background-color: $main-color;}
.nav-title{color: #fff; font-size: .28rem;display: inline-block;height: 100%; line-height: 1rem;margin-left: .5rem;}
.nav-more{display: inline-block;height: 100%;float: right;}

</style>
