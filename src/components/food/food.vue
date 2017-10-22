<template>
    <transition name="fade">
        <div class="food" v-show="showFlag" ref="food">
            <div class="food-content">
                <div class="img-header">
                    <img :src="food.image" >
                    <div class="back" @click="hide">X</div>
                </div>
                <div class="content">
                    <div class="title">{{food.name}}</div>
                    <div class="food-detail">
                        <span class="sell-count">月售{{food.sellCount}}份</span><span class="rating">好评率{{food.rating}}%</span>
                    </div>
                    <div class="price">
                        <span class="now">￥{{food.price}}</span><span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                    </div>
                    <div class="cartcontrol-wrapper">
                        <cartcontrol :food="food"></cartcontrol>
                    </div>
                    <transition name="addfirst">
                        <div class="buy" @click="addFirst" v-show="!food.count || food.count === 0">加入购物车</div>
                    </transition>
                </div>
                <split v-show="food.info"></split>
                <div class="info" v-show="food.info">
                    <div class="info-title">商品信息</div>
                    <p class="info-text">{{food.info}}</p>
                </div>
                <split></split>
                <div class="rating">
                    <h1 class="rating-title">商品评价</h1>
                    <ratingselect :ratings="food.ratings" :select-type="selectType" :only-content="onlyContent" :desc="desc"></ratingselect>
                </div>
                <div class="rating-wrapper">
                    <ul v-if="food.ratings && food.ratings.length">
                        <li v-show="needShow(rating.rateType, rating.text)" v-for="rating in food.ratings" class="rating-item">
                            <div class="user">
                                <span class="user-name">{{rating.username}}</span>
                                <img class="avatar" :src="rating.avatar" width="12" height="12">
                            </div>
                            <div class="time">{{rating.rateTime|formatDate}}</div>
                            <p class="rating-text">
                                <span class="thumb" :class="{'icon-up':rating.rateType === 0, 'icon-down':rating.rateType === 1}"></span>{{rating.text}}
                            </p>
                        </li>
                    </ul>
                    <div v-else class="no-rating">没有评论</div>
                </div>
            </div>
        </div>
    </transition>
</template>

<script>
import Vue from 'vue';
import BScroll from 'better-scroll';
import cartcontrol from '../cartcontrol/cartcontrol.vue';
import split from '../split/split.vue';
import { formatDate } from '../../common/js/date.js';
import ratingselect from '../ratingselect/ratingselect.vue';
import { eventBus } from '../event-bus.js';

const ALL = 2;
export default {
  props: {
      food: {
          type: Object
      }
  },
  data () {
      return {
          showFlag: false,
          selectType: ALL,
          onlyContent: true,
          desc: {
              all: '全部',
              positive: '推荐',
              negative: '吐槽'
          }
      };
  },
  filters: {
      formatDate (time) {
          let date = new Date(time);
          return formatDate(date, 'yyyy-MM-dd hh:mm');
      }
  },
  components: {
      cartcontrol,
      split,
      ratingselect
  },
  methods: {
      show () {
          this.showFlag = true;
          this.$nextTick(() => {
              if (!this.scroll) {
                  this.scroll = new BScroll(this.$refs.food, {
                      click: true
                  });
              } else {
                  this.scroll.refresh();
              }
          });
      },
      hide () {
          this.showFlag = false;
      },
      addFirst () {
          Vue.set(this.food, 'count', 1);
      },
      needShow (type, text) {
          if (this.onlyContent && !text) {
              return false;
          }
          if (this.selectType === ALL) {
              return true;
          } else {
              return type === this.selectType;
          }
      }
  },
  created () {
      eventBus.$on('ratingtype.select', (type) => {
          this.selectType = type;
          this.$nextTick(() => {
              this.scroll.refresh();
          });
      });
      eventBus.$on('ratingcontent.select', (onlyContent) => {
          this.onlyContent = onlyContent;
          this.$nextTick(() => {
              this.scroll.refresh();
          });
      });
  }
};
</script>

<style scoped>
.food{
    position: fixed;
    left: 0;
    top: 0;
    bottom: 48px;
    z-index: 30;
    width: 100%;
    background: #fff;
}
.food-content{
    position: relative;
}
.fade-enter-active, .fade-leave-active {
    transition: all .5s;
}
.fade-enter, .fade-leave-to{
    transform: translate3d(100%, 0, 0);
}
    .img-header{
        position: relative;
        width: 100%;
        height: 0;
        padding-top: 100%; 
    }
    .img-header>img{
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
    }
    .back{
        position: absolute;
        top: 10px;
        left: 10px;
        padding: 10px;
        font-size: 20px;
        color: #fff;
    }
    .content{
        position: relative;
        padding: 18px;
    }
        .title{
            line-height: 14px;
            margin-bottom: 8px;
            font-size: 14px;
            font-weight: 700;
            color: rgb(7, 17, 27);
        }
        .food-detail{
            margin-bottom: 18px;
            line-height: 10px;
            font-size: 0;
        }

        .sell-count,
        .rating {
            font-size: 10px;
            color: rgb(147, 153, 159);
        }

        .sell-count {
            margin-right: 12px;
        }

        .content .price {
            font-weight: 700;
            line-height: 24px;
        }

        .price .now {
            margin-right: 18px;
            font-size: 14px;
            color: rgb(240, 20, 20);
        }

        .price .old {
            text-decoration: line-through;
            font-size: 10px;
            color: rgb(147, 153, 159);
        }
    .cartcontrol-wrapper{
        position: absolute;
        right: 12px;
        bottom: 12px;
    }
        .buy{
            position: absolute;
            right: 18px;
            bottom: 18px;
            z-index: 10;
            height: 24px;
            line-height: 24px;
            padding: 0 12px;
            box-sizing: border-box;
            border-radius: 12px;
            font-size: 10px;
            color: #fff;
            background: rgb(0, 160 , 220);
        }
        .addfirst-enter-active, .addfirst-leave-active{
            transition: opacity .5s;
        }
        .addfirst-enter, .addfirst-leave-to{
            opacity: 0;
        }
    .info{
        padding: 18px;
    }
    .info-title{
        line-height: 14px;
        margin-bottom: 6px;
        font-size: 14px;
        color: rgb(7, 17, 27);
    }
    .info-text{
        padding: 0 8px;
        line-height: 24px;
        font-size: 12px;
        color: rgb(77, 85, 93);
    }
    .rating{
        padding-top: 18px;
    }
    .rating-title{
        line-height: 14px;
        margin-left: 18px;
        font-size: 14px;
        color: rgb(7, 17, 27);
    }
    .rating-wrapper{
        padding: 0 18px;
    }
    .rating-item{
        position: relative;
        padding: 16px 0;
        border-bottom: 1px solid rgba(7, 17, 27, .1);
    }
    .user{
        position: absolute;
        right: 0;
        top: 16px;
        font-size: 0;
        line-height: 12px;
    }
        .user-name{
            display: inline-block;
            margin-right: 6px;
            vertical-align: top;
            font-size: 10px;
            color: rgb(147, 153, 159);
        }
        .avatar{
            border-radius: 50%;
        }
    .time{
        margin-bottom: 6px;
        line-height: 12px;
        font-size: 10px;
        color: rgb(147, 153, 159);
    }
    .rating-text{
        line-height: 16px;
        font-size: 12px;
        color:rgb(7, 17, 27);
    }
        .thumb{
            display: inline-block;
            margin-right: 4px;
            width: 12px;
            height: 12px;
            line-height: 24px;
            background-repeat: no-repeat;
            background-size: 12px 12px;
            background-position: center;
        }
        .thumb.icon-up{
            background-image: url('thumb_up.png');
        }
        .thumb.icon-down{
            background-image: url('thumb_down.png');
        }
    .no-rating{
        padding: 16px 0;
        font-size: 12px;
        color: rgb(147, 153, 159);
    }
</style>


