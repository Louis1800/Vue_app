<template>
  <div class="goods">
    <div class="menu-wrapper" ref="menuWrapper">
      <ul>
        <li v-for="(item,index) in goods" class="menu-item" :class="{'current':currentIndex === index}" @click="selectMenu(index)">
          <span class="text">
            <span v-show="item.type > 0" class="icon" :class="classMap[item.type]"></span>
            {{item.name}}
          </span>
        </li>
      </ul>
    </div>
    <div class="foods-wrapper" ref="foodsWrapper">
      <ul>
        <li v-for="item in goods" class="food-list food-list-hook">
          <h1 class="title">{{item.name}}</h1>
          <ul>
            <li @click="selectenter(food,$event)" v-for="food in item.foods" class="food-item">
              <div class="icon">
                <img width="57" height="57" :src="food.icon">
              </div>
              <div class="content">
                <h2 class="foods-name">{{food.name}}</h2>
                <p class="desc">{{food.description}}</p>
                <div class="extra">
                  <span class="extra-s">月售{{food.sellCount}}份</span><span class="extra-s">好评率{{food.rating}}%</span>
                </div>
                <div class="price">
                  <span class="now">￥{{food.price}}</span><span class="old" v-show="food.oldPrice">￥{{food.oldPrice}}</span>
                </div>
                <div class="cartcontrol-wrapper">
                  <cartcontrol :food="food"></cartcontrol>
                </div>
              </div>
            </li>
          </ul>
        </li>
      </ul>
    </div>
    <shopcart :select-foods="selectFoods" :delivery-price="seller.deliveryPrice" :min-price="seller.minPrice"></shopcart>
    <food :food="selectedFood" ref="selectfood"></food>
  </div>
</template>

<script>
import BScroll from 'better-scroll';
import shopcart from '../shopcart/shopcart.vue';
import cartcontrol from '../cartcontrol/cartcontrol.vue';
import food from '../food/food.vue';

const ERR_OK = 0;

export default {
  props: {
    seller: {
      type: Object
    }
  },
  data () {
    return {
      goods: [],
      listHeight: [],
      scrollY: 0,
      selectedFood: {}
    };
  },
  computed: {
    currentIndex () {
      for (let i = 0; i < this.listHeight.length; i++) {
        let height1 = this.listHeight[i];
        let height2 = this.listHeight[i + 1];
        if (!height2 || (this.scrollY >= height1 && this.scrollY < height2)) {
          return i;
        }
      }
      return 0;
    },
    selectFoods () {
      let foods = [];
      this.goods.forEach(function (good) {
        good.foods.forEach((food) => {
          if (food.count) {
            foods.push(food);
          }
        });
      }, this);
      return foods;
    }
  },
  created () {
    this.classMap = ['decrease', 'discount', 'special', 'invoice', 'guarantee'];
    this.$http.get('api/goods').then((response) => {
      response = response.body;
      if (response.errno === ERR_OK) {
        this.goods = response.data;
        this.$nextTick(() => {
            this._initScroll();
            this._calculateHeight();
        });
      }
    });
  },
  methods: {
    selectMenu (index) {
      let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook');
      let el = foodList[index];
      this.foodsScroll.scrollToElement(el, 300);
    },
    _initScroll () {
      this.menuScroll = new BScroll(this.$refs.menuWrapper, {
        click: true
      });
      this.foodsScroll = new BScroll(this.$refs.foodsWrapper, {
        click: true,
        probeType: 3
      });
      this.foodsScroll.on('scroll', (pos) => {
        this.scrollY = Math.abs(Math.round(pos.y));
      });
    },
    _calculateHeight () {
      let foodList = this.$refs.foodsWrapper.getElementsByClassName('food-list-hook');
      let height = 0;
      this.listHeight.push(height);
      for (let i = 0; i < foodList.length; i++) {
        let item = foodList[i];
        height += item.clientHeight;
        this.listHeight.push(height);
      }
    },
    selectenter (food, event) {
      this.selectedFood = food;
      this.$refs.selectfood.show();
    }
  },
  components: {
    shopcart,
    cartcontrol,
    food
  }
};
</script>

<style scoped>
.goods {
  display: flex;
  position: absolute;
  top: 175px;
  bottom: 46px;
  width: 100%;
  overflow: hidden;
}

.menu-wrapper {
  flex: 0 0 80px;
  width: 80px;
  background: #f3f5f7;
}
  .menu-wrapper .menu-item{
    display: table;
    width: 56px;
    height: 54px;
    padding: 0 12px;
    line-height: 14px;
  }
    .menu-item.current{
      position: relative;
      z-index: 10;
      margin-top: -1px;
      background: #fff;
      font-weight: 700;
    }
    .menu-item.current>.text{
      border-bottom: none;
    }
    .menu-item .icon{
      display: inline-block;
      width: 12px;
      height: 12px;
      vertical-align: top;
      margin-right: 2px;
      background-size: 12px 12px;
      background-repeat: no-repeat;
    }
    
    .menu-item .icon.decrease {
      background-image: url("decrease_3@2x.png");
    }

    .menu-item .icon.discount {
      background-image: url("discount_3@2x.png");
    }

    .menu-item .icon.guarantee {
      background-image: url("guarantee_3@2x.png");
    }

    .menu-item .icon.invoice {
      background-image: url("invoice_3@2x.png");
    }

    .menu-item .icon.special {
      background-image: url("special_3@2x.png");
    }

    @media(min-device-pixel-ratio:3) {
      .menu-item .icon.decrease {
        background-image: url("decrease_3@3x.png");
      }
      .menu-item .icon.discount {
        background-image: url("discount_3@3x.png");
      }
      .menu-item .icon.guarantee {
        background-image: url("guarantee_3@3x.png");
      }
      .menu-item .icon.invoice {
        background-image: url("invoice_3@3x.png");
      }
      .menu-item .icon.special {
        background-image: url("special_3@3x.png");
      }
    }
    .menu-item .text{
      display: table-cell;
      width: 56px;
      vertical-align: middle;
      font-size: 12px;
      border-bottom: 1px solid rgba(7, 17, 27, .1);
    }

.foods-wrapper {
  flex: 1;
}
  .food-list>.title{
    padding-left: 14px;
    height: 26px;
    line-height: 26px;
    border-left: 2px solid #d9dde1;
    font-size: 12px;
    color: rgb(147, 153, 159);
    background: #f3f5f7;
  }
  .food-list .food-item{
    display: flex;
    position: relative;
    margin: 18px;
    padding-bottom: 18px;
    border-bottom: 1px solid rgba(7, 17, 27, .1);
  }
  .food-list .food-item:last-child{
    margin-bottom: 0;
    border-bottom: 0;
  }
  .food-item>.icon{
    flex: 0 0 57px;
    margin-right: 10px;
  }
  .food-item .content{
    flex: 1;
  }
  .content .foods-name{
    margin: 2px 0 8px 0;
    height: 14px;
    line-height: 14px;
    font-size: 14px;
    color: rgb(7, 17, 27);
  }
  .content .desc,.content .extra{
    font-size: 10px;
    color: rgb(147, 153, 159);
  }
  .content .desc{
    line-height: 12px;
    margin-bottom: 8px;
  }
  .extra .extra-s{
    margin-right: 12px;
  }
  .content .price{
    font-weight: 700;
    line-height: 24px;
  }
  .price .now{
    margin-right: 18px;
    font-size: 14px;
    color: rgb(240, 20, 20);
  }
  .price .old{
    text-decoration: line-through;
    font-size: 10px;
    color: rgb(147, 153, 159);
  }
  .cartcontrol-wrapper{
    position: absolute;
    right: 0;
    bottom: 12px;
  }
</style>
