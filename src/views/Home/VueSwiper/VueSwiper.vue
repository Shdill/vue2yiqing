<template>
  <div>
    <swiper ref="mySwiper" :options="swiperOptions">
      <swiper-slide v-for="item in banner" :key='item.title'>
        <img :src="item.image" alt="" width="100%" />
      </swiper-slide>
      <div class="swiper-pagination" slot="pagination"></div>
    </swiper>
    <!-- 轮播的图片按钮 -->
    <ul class="list">
      <li
        v-for="(item,index) in banner" :key='item.title'
        :class="{ active: index == num }"
        @click="changeSwiper(index)"
      >
        {{ item.title }}
      </li>
    </ul>
  </div>
</template>

<script>
import api from '../../../api/index'
export default {
  data() {
      //接受this 
      let that = this;
    return {
      num: 0,
      arr: [1, 2, 3, 4, 5],
      banner:[],//轮播图数据
      //swiper配置内容
      swiperOptions: {
        pagination: {
          el: ".swiper-pagination",
        },
        autoplay: {
          delay: 3000,
          stopOnLastSlide: false,
          disableOnInteraction: false,
        },
        loop: true,
        on: {
            //回调函数，swiper从一个slide过渡到另一个slide开始时执行
          slideChangeTransitionStart: function () {
            //   console.log('轮播切换了---',this.activeIndex);//1 - 2 3 4 5 6 
              //修改num=[0,1,2,3,4]
              //轮播切换的时候获取下标activeIndex  然后修改num同步切换高亮
              //判断：
              if(this.activeIndex == that.arr.length+1){
                    that.num =0;
              }else{
                  that.num = this.activeIndex-1;
              }
          },
        },
      },
    };
  },
  computed: {
    //获取swiper实例对象
    swiper() {
      return this.$refs.mySwiper.$swiper;
    },
  },
  mounted() {
      api.getSwiperBanner()
      .then(res=>{
          console.log('--轮播图--',res.data);
          this.banner = res.data.result;
      })
  },
  methods: {
    changeSwiper(index) {
      //高亮选中
      this.num = index;
      //让swiper切换
      this.swiper.slideTo(index + 1, 1000, false);
    },
  },
};
</script>

<style lang='less' scoped>
.list {
  display: flex;
  li {
    flex: 1;
    padding: 0.1rem;
    font-size: 0.24rem;
    line-height: 0.32rem;
    margin: 0.1rem;
    background: #eee;
    color: #666;
    text-align: center;
    display: flex;
    align-items: center;
  }
  .active {
    background: rgb(80, 116, 173);
    color: #fff;
  }
}
</style>