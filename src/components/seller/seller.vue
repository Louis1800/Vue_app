<template>
  <div class="seller" ref="seller">
    <div class="seller-content">
      <div class="overview">
        <h1 class="title">{{seller.name}}</h1>
        <div class="desc">
          <v-star class="star" :size="36" :score="seller.score"></v-star>
          <span class="text">({{seller.ratingCount}})</span>
          <span class="text">月售{{seller.sellCount}}单</span>
        </div>
        <ul class="remark">
          <li class="block">
            <h2>起送价</h2>
            <div class="remark-content">
              <span class="stress">{{seller.minPrice}}</span>元
            </div>
          </li>
          <li class="block">
            <h2>商家配送</h2>
            <div class="remark-content">
              <span class="stress">{{seller.deliveryPrice}}</span>元
            </div>
          </li>
          <li class="block">
            <h2>平均配送时间</h2>
            <div class="remark-content">
              <span class="stress">{{seller.deliveryTime}}</span>元
            </div>
          </li>
        </ul>
        <div class="favorite" @click="toggleFavorite">
          <span class="icon-favorite" :class="{'active': favorite}"></span>
          <span class="icon-text">{{favoriteText}}</span>
        </div>
      </div>
      <split></split>
      <div class="bulletin">
        <h1 class="bul-title">公告与活动</h1>
        <div class="bul-content-wrapper">
          <p class="bul-content">{{seller.bulletin}}</p>
        </div>
        <ul v-if="seller.supports" class="detail-supports">
            <li v-for="(item,index) in seller.supports" class="support-item">
              <span class="support-icon" :class="classMap[seller.supports[index].type]"></span>
              <span class="support-text">{{seller.supports[index].description}}</span>
            </li>
          </ul>
      </div>
      <split></split>
      <div class="pics">
        <h1 class="pics-title">商家实景</h1>
        <div class="pic-wrapper" ref="picWrapper">
          <ul class="pic-list" ref="picList">
            <li class="pic-item" v-for="pic in seller.pics">
              <img :src="pic" width="120" height="90">
            </li>
          </ul>
        </div>
      </div>
      <split></split>
      <div class="info">
        <h1 class="info-title">商家信息</h1>
        <ul>
          <li class="info-item" v-for="info in seller.infos">{{info}}</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>
import BScroll from 'better-scroll';
import star from '../star/star.vue';
import split from '../split/split.vue';
import { saveToLocal, loadFromLocal } from '../../common/js/store.js';

export default {
  props: {
    seller: {
      type: Object
    }
  },
  data () {
    return {
      favorite: (() => {
        return loadFromLocal(this.seller.id, 'favorite', false);
      })()
    };
  },
  computed: {
    favoriteText () {
      return this.favorite ? '收藏' : '未收藏';
    }
  },
  components: {
    'v-star': star,
    split
  },
  watch: {
    'seller' () {
      this.$nextTick(() => {
        this._initScroll();
        this._initPics();
      });
    }
  },
  mounted () {
    this.$nextTick(() => {
      this._initScroll();
      this._initPics();
    });
  },
  created () {
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
  },
  methods: {
    toggleFavorite () {
      this.favorite = !this.favorite;
      saveToLocal(this.seller.id, 'favorite', this.favorite);
    },
    _initScroll () {
      if (!this.scroll) {
        this.scroll = new BScroll(this.$refs.seller, {
          click: true
        });
      } else {
        this.scroll.refresh();
      }
    },
    _initPics () {
      if (this.seller.pics) {
        let picWidth = 120;
        let margin = 6;
        let width = (picWidth + margin) * this.seller.pics.length - margin;
        this.$refs.picList.style.width = width + 'px';
        this.$nextTick(() => {
          if (!this.picScroll) {
            this.picScroll = new BScroll(this.$refs.picWrapper, {
              scrollX: true,
              eventPassthrough: 'vertical'
            });
          } else {
            this.picScroll.refresh();
          }
        });
      }
    }
  }
};
</script>

<style scoped>
.seller {
  position: absolute;
  top: 174px;
  bottom: 0;
  left: 0;
  width: 100%;
  overflow: hidden;
}
.overview {
  position: relative;
  padding: 18px;
}
.favorite{
  position: absolute;
  right: 18px;
  top: 18px;
  text-align: center
}
.icon-favorite{
  display: block;
  margin-bottom: 4px;
  width: 36px;
  height: 24px;
  line-height: 24px;
  font-size: 24px;
  background-image: url('favorite.png');
  background-position: center;
  background-size: 24px 24px;
  background-repeat: no-repeat;
}
.icon-favorite.active{
  background-image: url('favoritex.png');
}
.icon-text{
  line-height: 10px;
  font-size: 10px;
  color: rgb(77, 85, 93);
}
.title {
  margin-bottom: 8px;
  line-height: 14px;
  color: rgb(7, 17, 27);
  font-size: 14px;
}
.desc {
  padding-bottom: 18px;
  line-height: 16px;
  font-size: 0;
  border-bottom: 1px solid rgba(7, 17, 27, 0.1);
}
.star {
  display: inline-block;
  margin-right: 8px;
  vertical-align: top;
}
.text {
  display: inline-block;
  margin-right: 12px;
  vertical-align: top;
  font-size: 10px;
  color: rgb(77, 85, 93);
}
.remark {
  display: flex;
  padding-top: 18px;
}
.block {
  flex: 1;
  text-align: center;
  border-right: 1px solid rgba(7, 17, 27, 0.1);
}
.block:last-child {
  border: none;
}
.block > h2 {
  margin-bottom: 4px;
  line-height: 10px;
  font-size: 10px;
  color: rgb(147, 153, 159);
}
.remark-content {
  line-height: 24p;
  font-size: 10px;
  color: rgb(7, 17, 27);
}
.stress {
  font-size: 24px;
}
.bulletin {
  padding: 18px 18px 0 18px;
}
.bul-title {
  margin-bottom: 8px;
  line-height: 14px;
  color: rgb(7, 17, 27);
  font-size: 14px;
}
.bul-content-wrapper {
  padding: 0 12px 16px 12px;
  border-bottom: 1px solid rgba(7, 17, 27, 0.1);
}
.bul-content {
  line-height: 24px;
  font-size: 12px;
  color: rgb(240, 20, 20);
}
.detail-supports {
  width: 100%;
}
.support-item {
  padding: 16px 12px;
  margin-bottom: 0;
  border-bottom: 1px solid rgba(7, 17, 27, 0.1);
  font-size: 0;
}
.support-item:last-child {
  border: none;
}
.support-icon {
  display: inline-block;
  width: 16px;
  height: 16px;
  vertical-align: top;
  margin-right: 6px;
  background-size: 16px 16px;
  background-repeat: no-repeat;
}

.decrease {
  background-image: url("decrease_4@2x.png");
}

.discount {
  background-image: url("discount_4@2x.png");
}

.guarantee {
  background-image: url("guarantee_4@2x.png");
}

.invoice {
  background-image: url("invoice_4@2x.png");
}

.special {
  background-image: url("special_4@2x.png");
}

@media (min-device-pixel-ratio: 3) {
  .decrease {
    background-image: url("decrease_4@3x.png");
  }
  .discount {
    background-image: url("discount_4@3x.png");
  }
  .guarantee {
    background-image: url("guarantee_4@3x.png");
  }
  .invoice {
    background-image: url("invoice_4@3x.png");
  }
  .special {
    background-image: url("special_4@3x.png");
  }
}
.support-text {
  vertical-align: top;
  line-height: 16px;
  font-size: 12px;
  color: rgb(7, 17, 27);
}
.pics {
  padding: 18px;
}
.pics-title {
  margin-bottom: 12px;
  line-height: 14px;
  color: rgb(7, 17, 27);
  font-size: 14px;
}
.pic-wrapper {
  width: 100%;
  overflow: hidden;
  white-space: nowrap;
}
.pic-list {
  font-size: 0;
}
.pic-item {
  display: inline-block;
  margin-right: 6px;
  width: 120px;
  height: 90px;
}
.pic-item:last-child {
  margin: 0;
}
.info{
  padding: 18px 18px 0 18px;
  color: rgb(7, 17, 27);
}
.info-title{
  padding-bottom: 12px;
  line-height: 14px;
  border-bottom: 1px solid rgba(7, 17, 27, 0.1);
  color: rgb(7, 17, 27);
  font-size: 14px;
}
.info-item{
  padding: 16px 12px;
  line-height: 16px;
  border-bottom: 1px solid rgba(7, 17, 27, 0.1);
  font-size: 12px;
}
.info-item:last-child{
  border: none;
}
</style>
