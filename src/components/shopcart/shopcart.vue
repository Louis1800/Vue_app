<template>
<div>
    <div class="shopcart">
        <div class="content" @click="toggleList">
            <div class="content-left">
                <div class="logo-wrapper">
                    <div class="logo" :class="{'highlight': totalCount > 0}">
                        <div class="cart-icon"></div>
                    </div>
                    <div class="num" v-show="totalCount > 0">{{totalCount}}</div>
                </div>
                <div class="price" :class="{'highlight': totalPrice > 0}">{{totalPrice}}元</div>
                <div class="desc">另需配送费￥{{deliveryPrice}}元</div>
            </div>
            <div class="content-right">
                <div class="pay" :class="payClass" @click.stop="pay">
                    {{payDesc}}
                </div>
            </div>
        </div>
        <!-- <div class="ball-container">
            <div transition="drop" v-for="ball in balls" v-show="ball.show" class="ball">
                <div class="inner inner-hook"></div>
            </div>
        </div> -->
        <div class="shopcart-list" v-show="listShow">
            <div class="list-header">
                <h1 class="title">购物车</h1>
                <span class="empty" @click="empty">清空</span>
            </div>
            <div class="list-content" ref="listContent">
                <ul>
                    <li class="food" v-for="food in selectFoods">
                        <span class="name">{{food.name}}</span>
                        <div class="price">
                            <span>￥{{food.price * food.count}}</span>
                        </div>
                        <div class="cartcontrol-wrapper">
                            <cartcontrol :food="food"></cartcontrol>
                        </div>
                    </li>
                </ul>
            </div>
        </div>
    </div>
    <div class="list-mask" @click="hideList" v-show="listShow"></div>
</div>
</template>

<script>
// import { eventBus } from '../event-bus.js';
import cartcontrol from '../cartcontrol/cartcontrol.vue';
import BScroll from 'better-scroll';

export default {
    props: {
        selectFoods: {
            type: Array,
            default () {
                return [
                    {
                        price: 0,
                        count: 0
                    }
                ];
            }
        },
        deliveryPrice: {
            type: Number,
            default: 0
        },
        minPrice: {
            type: Number,
            default: 0
        }
    },
    data () {
        return {
            fold: true
        };
    },
    // data () {
    //     return {
    //         balls: [
    //             {
    //                 show: false
    //             },
    //             {
    //                 show: false
    //             },
    //             {
    //                 show: false
    //             },
    //             {
    //                 show: false
    //             },
    //             {
    //                 show: false
    //             }
    //         ],
    //         dropBalls: []
    //     };
    // },
    computed: {
        totalPrice () {
            let total = 0;
            this.selectFoods.forEach((food) => {
                total += food.price * food.count;
            });
            return total;
        },
        totalCount () {
            let count = 0;
            this.selectFoods.forEach((food) => {
                count += food.count;
            });
            return count;
        },
        payDesc () {
            if (this.totalPrice === 0) {
                return `￥${this.minPrice}元起送`;
            } else if (this.totalPrice < this.minPrice) {
                let diff = this.minPrice - this.totalPrice;
                return `还差${diff}元起送`;
            } else {
                return '去结算';
            }
        },
        payClass () {
            if (this.totalPrice < this.minPrice) {
                return 'not-enough';
            } else {
                return 'enough';
            }
        },
        listShow () {
            if (!this.totalCount) {
                this.fold = true;
                return false;
            }
            let show = !this.fold;
            if (show) {
                this.$nextTick(() => {
                    if (!this.scroll) {
                        this.scroll = new BScroll(this.$refs.listContent, {
                            click: true
                        });
                    } else {
                        this.scroll.refresh();
                    }
                });
            }
            return show;
        }
    },
    methods: {
        toggleList () {
            if (!this.totalCount) {
                return;
            }
            this.fold = !this.fold;
        },
        empty () {
            this.selectFoods.forEach((food) => {
                food.count = 0;
            });
        },
        hideList () {
            this.fold = true;
        },
        pay () {
            if (this.totalPrice < this.minPrice) {
                return;
            }
            window.alert(`请支付${this.totalPrice}元`);
        }
    },
    // created () {
    //     eventBus.$on('cart.add', (el) => {
    //         for (let i = 0; i < this.balls.length; i++) {
    //             let ball = this.balls[i];
    //             if (!ball.show) {
    //                 ball.show = true;
    //                 ball.el = el;
    //                 this.dropBalls.push(ball);
    //                 return;
    //             }
    //         };
    //     });
    // },
    // transition: {
    //     drop: {
    //         beforeEnter (el) {
    //             let count = this.balls.length;
    //             while (count--) {
    //                 let ball = this.balls[count];
    //                 if (ball.show) {
    //                     let rect = ball.el.getBoundingClientRect();
    //                     let x = rect.left - 32;
    //                     let y = -(window.innerHeight - rect.top - 22);
    //                     el.style.display = '';
    //                     el.style.webkitTransform = `translate3d(0,${y}px,0)`;
    //                     el.style.transform = `translate3d(0,${y}px,0)`;
    //                     let inner = el.getElementsByClassName('inner-hook')[0];
    //                     inner.style.webkitTransform = `translate3d(${x}px,0,0)`;
    //                     inner.style.transform = `translate3d(${x}px,0,0)`;
    //                 }
    //             }
    //         },
    //         enter (el) {
    //             /* eslint-disable no-unused-vars */
    //             let rf = el.offsetHeight;
    //             this.$nextTick(() => {
    //                 el.style.webkitTransform = 'translate3d(0,0,0)';
    //                 el.style.transform = 'translate3d(0,0,0)';
    //                 let inner = el.getElementsByClassName('inner-hook')[0];
    //                 inner.style.webkitTransform = 'translate3d(0,0,0)';
    //                 inner.style.transform = 'translate3d(0,0,0)';
    //             });
    //         },
    //         afterEnter (el) {
    //             let ball = this.dropBalls.shift();
    //             if (ball) {
    //                 ball.show = false;
    //                 el.style.display = 'none';
    //             }
    //         }
    //     }
    // },
    components: {
        cartcontrol
    }
};
</script>

<style scoped>
.shopcart{
    position: fixed;
    left: 0;
    bottom: 0;
    z-index: 50;
    width: 100%;
    height: 48px;
}
    .content{
        display: flex;
        background: #141d27;
        font-size: 0;
        color: rgba(255, 255, 255, .4);
    }
        .content-left{
            flex: 1;
        }
            .logo-wrapper,.price,.desc{
                display: inline-block;
                vertical-align: top;
            }
            .logo-wrapper{
                position: relative;
                top: -10px;
                margin: 0 12px;
                padding: 6px;
                width: 56px;
                height: 56px;
                box-sizing: border-box;
                border-radius: 50%;
                background: #141d27;
            }
                .logo{
                    width: 100%;
                    height: 100%;
                    border-radius: 50%;
                    background: rgba(255, 255, 255, .2);
                }
                .logo.highlight{
                    background: rgba(0, 160, 220, 1);
                }
                .cart-icon{
                    width: 44px;
                    height: 44px;
                    background-size: 50% 50%;
                    background-position: center;
                    background-image: url(shopping_cart.png);
                    background-repeat: no-repeat;
                }
                .num{
                    position: absolute;
                    top: 0;
                    right: 0;
                    width: 24px;
                    height: 16px;
                    line-height: 16px;
                    text-align: center;
                    border-radius: 16px;
                    font-size: 9px;
                    font-weight: 700;
                    color: #fff;
                    background: rgb(240, 20, 20);
                    box-shadow: 0 4px 8px 0 rgba(0, 0, 0, .4);
                }
            .price{
                margin-top: 12px;
                line-height: 24px;
                padding-right: 12px;
                box-sizing: border-box;
                border-right: 1px solid rgba(255, 255, 255, .1);
                font-size: 16px;
                font-weight: 700;
            }
            .price.highlight{
                color: #fff;
            }
            .desc{
                margin: 12px 0 0 12px;
                line-height: 24px;
                font-size: 10px;
            }
        .content-right{
            flex: 0 0 105px;
            width: 105px;
        }
            .pay{
                height: 48px;
                line-height: 48px;
                text-align: center;
                font-size: 12px;
            }
            .not-enough{
                background: rgba(255, 255, 255, .2);
            }
            .enough{
                background: #00b43c;
                color: #fff;
            }
    .shopcart-list{
        position: absolute;
        left: 0;
        bottom: 48px;
        z-index: -1;
        width: 100%;
    }
    .list-header{
        height: 40px;
        line-height: 40px;
        padding: 0 18px;
        background: #f3f5f7;
        border-bottom: 1px solid rgba(7, 17, 27, .1);
    }
        .title{
            float: left;
            font-size: 14px;
            color: rgb(7, 17, 27);
        }
        .empty{
            float: right;
            font-size: 12px;
            color: rgb(0, 160, 220)
        }
    .list-content{
        padding: 0 18px;
        max-height: 217px;
        overflow: hidden;
        background: #fff;
        
    }
        .list-content .food{
            position: relative;
            padding: 12px 0;
            box-sizing: border-box;
            border-bottom: 1px solid rgba(7, 17, 27, .1);
        }
        .list-content .name{
            line-height: 24px;
            font-size: 14px;
            color: rgb(7, 17, 27);
        }
        .list-content .price{
            position: absolute;
            right: 90px;
            bottom: 12px;
            line-height: 24px;
            font-size: 14px;
            font-weight: 700;
            color: rgb(240, 20, 20);
        }
        .cartcontrol-wrapper{
            position: absolute;
            right: 0;
            bottom: 6px;
        }
.list-mask{
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    z-index: 40;
    background: rgba(7, 17, 27, .6);
    -webkit-backdrop-filter: blur(10px);
}
    /* .ball-container{
    } */
    /* .ball{
        position: fixed;
        left: 32px;
        bottom: 22px;
        z-index: 200;
    }
    .ball.drop-transition{
        transition: all 0.4s;
    }
    .drop-transition .inner{
        width: 16px;
        height: 16px;
        border-radius: 50%;
        background: rgb(0, 160, 220);
        transition: all 0.4s;
    } */
</style>
