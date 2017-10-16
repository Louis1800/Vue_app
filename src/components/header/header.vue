<template>
  <div class="header">
    <div class="content-wrapper">
      <div class="avatar">
        <img width="64" height="64" :src="seller.avatar" alt="">
      </div>
      <div class="content">
        <div class="title">
          <span class="brand"></span>
          <span class="name">{{seller.name}}</span>
        </div>
        <div class="description">
          {{seller.description}}/{{seller.deliveryTime}}分钟送达
        </div>
        <div v-if="seller.supports" class="support">
          <span class="icon" :class="classMap[seller.supports[0].type]"></span>
          <span class="text">{{seller.supports[0].description}}</span>
        </div>
      </div>
      <div v-if="seller.supports" class="support-count" @click="showDetail">
        <span class="count">{{seller.supports.length}}个</span>
        <i class="icon-keyboard_arrow_right"></i>
      </div>
    </div>
    <div class="bulletin-wrapper" @click="showDetail">
      <span class="bulletin-title"></span><span class="bulletin-text">{{seller.bulletin}}</span>
      <i class="icon-keyboard_arrow_right bulletin-arrow"></i>
    </div>
    <div class="background">
      <img :src="seller.avatar" width="100%" height="100%">
    </div>
    <div v-show="detailShow" class="detail">
      <div class="detail-wrapper clearfix">
        <div class="detail-main">
          <h1 class="detail-name">{{seller.name}}</h1>
          <div class="star-wrapper">
            <v-star :size="48" :score="seller.score"></v-star>
          </div>
          <div class="detail-title">
            <div class="detail-line"></div>
            <div class="detail-text">优惠信息</div>
            <div class="detail-line"></div>
          </div>
          <ul v-if="seller.supports" class="detail-supports">
            <li v-for="(item,index) in seller.supports" class="support-item">
              <span class="support-icon" :class="classMap[seller.supports[index].type]"></span>
              <span class="support-text">{{seller.supports[index].description}}</span>
            </li>
          </ul>
          <div class="detail-title">
            <div class="detail-line"></div>
            <div class="detail-text">商家公告</div>
            <div class="detail-line"></div>
          </div>
          <div class="detail-bulletin">
            <p class="detail-content">{{seller.bulletin}}</p>
          </div>
          
        </div>
      </div>
      <div class="detail-close" @click="showDetail">
        X
      </div>
    </div>
  </div>
</template>

<script>
import star from '../star/star.vue';
export default {
  props: {
    seller: {
      type: Object
    }
  },
  data () {
    return {
      detailShow: false
    };
  },
  methods: {
    showDetail () {
      this.detailShow = !this.detailShow;
    }
  },
  created () {
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
  },
  components: {
    'v-star': star
  }
};
</script>

<style>
.header {
  position: relative;
  overflow: hidden;
  color: #fff;
  background: rgba(7, 17, 27, .5);
}

.header .content-wrapper {
  position: relative;
  padding: 24px 12px 18px 24px;
  font-size: 0;
}

.header .avatar,
.header .content {
  display: inline-block;
}

.header .avatar {
  vertical-align: top;
}

.header img {
  border-radius: 2px;
}

.header .content {
  margin-left: 16px;
  font-size: 14px;
}

.header .title {
  margin: 2px 0 8px 0;
}

.header .brand {
  display: inline-block;
  vertical-align: top;
  width: 30px;
  height: 18px;
  background-image: url("brand@2x.png");
  background-size: 30px 18px;
  background-repeat: no-repeat;
}

@media(min-device-pixel-ratio:3) {
  .header .brand {
    background-image: url("brand@3x.png")
  }
}

.header .name {
  margin-left: 6px;
  font-size: 16px;
  line-height: 18px;
  font-weight: bold;
}

.header .description {
  margin-bottom: 10px;
  line-height: 12px;
  font-size: 12px;
}

.header .icon {
  display: inline-block;
  width: 12px;
  height: 12px;
  margin-right: 4px;
  background-size: 12px 12px;
  background-repeat: no-repeat;
}

.header .decrease {
  background-image: url("decrease_1@2x.png");
}

.header .discount {
  background-image: url("discount_1@2x.png");
}

.header .guarantee {
  background-image: url("guarantee_1@2x.png");
}

.header .invoice {
  background-image: url("invoice_1@2x.png");
}

.header .special {
  background-image: url("special_1@2x.png");
}

@media(min-device-pixel-ratio:3) {
  .header .decrease {
    background-image: url("decrease_1@3x.png");
  }
  .header .discount {
    background-image: url("discount_1@3x.png");
  }
  .header .guarantee {
    background-image: url("guarantee_1@3x.png");
  }
  .header .invoice {
    background-image: url("invoice_1@3x.png");
  }
  .header .special {
    background-image: url("special_1@3x.png");
  }
}

.header .text {
  vertical-align: top;
  line-height: 12px;
  font-size: 10px;
}

.header .support-count {
  position: absolute;
  right: 12px;
  bottom: 14px;
  padding: 0 8px;
  height: 24px;
  line-height: 24px;
  border-radius: 14px;
  background: rgba(0, 0, 0, .2);
  text-align: center;
}

.header .count {
  vertical-align: top;
  font-size: 10px;
}

.header .icon-keyboard_arrow_right {
  margin-left: 2p;
}

.icon-keyboard_arrow_right::before {
  content: url("keyboard_arrow_right.png");
}

.header .bulletin-wrapper {
  position: relative;
  height: 28px;
  line-height: 28px;
  padding: 0 22px 0 12px;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  background: rgba(7, 17, 27, .2);
}

.header .bulletin-title {
  display: inline-block;
  vertical-align: top;
  margin-top: 8px;
  width: 22px;
  height: 12px;
  background-image: url("bulletin@2x.png");
  background-size: 22px 12px;
  background-repeat: no-repeat;
}

@media(min-device-pixel-ratio:3) {
  .header .bulletin-title {
    background-image: url("bulletin@3x.png");
  }
}

.header .bulletin-text{
  margin: 0 4px;
  vertical-align: top;
  font-size: 10px;
}

.header .bulletin-arrow{
  position: absolute;
  right: 0;
  top: 3px;
}

.background{
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: -1;
  filter: blur(10px);
}

.detail{
  position: fixed;
  top: 0;
  left: 0;
  z-index: 100;
  width: 100%;
  height: 100%;
  overflow: auto;
  background: rgba(7, 17, 27, .8);
  -webkit-backdrop-filter: blur(10px);
}

/* 浮层 */
.detail-wrapper{
  width: 100%;
  min-height: 100%;
}

.detail-main{
  margin-top: 64px;
  padding-bottom: 64px;
}
.detail-title{
  display: flex;
  width: 80%;
  margin: 28px auto 24px auto;
}
.detail-line{
  flex: 1;
  position: relative;
  top: -6px;
  border-bottom: 1px solid rgba(255, 255, 255, .2);
}
.detail-text{
  padding: 0 12px;
  font-weight: 700;
  font-size: 14px;
}
.detail-close{
  position: relative;
  width: 32px;
  height: 32px;
  margin: -64px auto 0 auto;
  clear: both;
  font-size: 32px;
}

.clearfix{
  display: inline-block;
}

.clearfix::after{
  display: block;
  content: ".";
  height: 0;
  line-height: 0;
  clear: both;
  visibility: hidden;
}

.detail-name{
  line-height: 16px;
  text-align: center;
  font-size: 16px;
  font-weight: 700;
}

.star-wrapper{
  margin-top: 18px;
  padding: 2px 0;
  text-align: center;
}

.detail-supports{
  width: 80%;
  margin: 0 auto;
}

.support-item{
  padding: 0 12px;
  margin-bottom: 12px;
  font-size: 0;
}

.support-item:last-child{
  margin-bottom: 0;
}

.support-icon{
  display: inline-block;
  width: 16px;
  height: 16px;
  vertical-align: top;
  margin-right: 6px;
  background-size: 16px;
  background-repeat: no-repeat;
}

.detail-supports .decrease {
  background-image: url("decrease_2@2x.png");
}

.detail-supports .discount {
  background-image: url("discount_2@2x.png");
}

.detail-supports .guarantee {
  background-image: url("guarantee_2@2x.png");
}

.detail-supports .invoice {
  background-image: url("invoice_2@2x.png");
}

.detail-supports .special {
  background-image: url("special_2@2x.png");
}

@media(min-device-pixel-ratio:3) {
  .detail-supports .decrease {
    background-image: url("decrease_2@3x.png");
  }
  .detail-supports .discount {
    background-image: url("discount_2@3x.png");
  }
  .detail-supports .guarantee {
    background-image: url("guarantee_2@3x.png");
  }
  .detail-supports .invoice {
    background-image: url("invoice_2@3x.png");
  }
  .detail-supports .special {
    background-image: url("special_2@3x.png");
  }
}

.support-text{
  line-height: 16px;
  font-size: 12px;
}

.detail-bulletin{
  width: 80%;
  margin: 0 auto;
}

.detail-content{
  padding: 0 12px;
  line-height: 24px;
  font-size: 12px;
}

</style>

