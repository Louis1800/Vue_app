<template>
    <div class="ratingselect">
        <div class="rating-type">
            <span @click="select(2)" class="block positive" :class="{'active':selectTypeInner === 2}">{{desc.all}}<span class="ratings-count">{{ratings.length}}</span></span>
            <span @click="select(0)" class="block positive" :class="{'active':selectTypeInner === 0}">{{desc.positive}}<span class="ratings-count">{{positives.length}}</span></span>
            <span @click="select(1)" class="block negative" :class="{'active':selectTypeInner === 1}">{{desc.negative}}<span class="ratings-count">{{negatives.length}}</span></span>
        </div>
        <div @click="toggleContent" class="switch">
            <span class="switch-icon" :class="{'on':onlyContentInner === true}"></span>
            <span class="switch-text">只看有内容的评价</span>
        </div>
    </div>
</template>

<script>
import { eventBus } from '../event-bus.js';
const POSITIVE = 0;
const NEGATIVE = 1;
const ALL = 2;

export default {
    data () {
        return {
            selectTypeInner: this.selectType,
            onlyContentInner: this.onlyContent
        };
    },
    props: {
        ratings: {
            type: Array,
            default () {
                return [];
            }
        },
        selectType: {
            type: Number,
            default: ALL
        },
        onlyContent: {
            type: Boolean,
            default: false
        },
        desc: {
            type: Object,
            default () {
                return {
                    all: '全部',
                    positive: '满意',
                    negative: '不满意'
                };
            }
        }
    },
    methods: {
        select (type) {
            // console.log('select');
            this.selectTypeInner = type;
            eventBus.$emit('ratingtype.select', type);
        },
        toggleContent () {
            // console.log('toggle');
            this.onlyContentInner = !this.onlyContentInner;
            eventBus.$emit('ratingcontent.select', this.onlyContentInner);
        }
    },
    computed: {
        positives () {
            return this.ratings.filter((rating) => {
                return rating.rateType === POSITIVE;
            });
        },
        negatives () {
            return this.ratings.filter((rating) => {
                return rating.rateType === NEGATIVE;
            });
        }
    }
};
</script>

<style scoped>
.rating-type{
    padding: 18px 0;
    margin: 0 18px;
    border-bottom: 1px solid rgba(7, 17, 27, .1);
    font-size: 0;
}
    .block{
        display: inline-block;
        padding: 8px 12px;
        margin-right: 8px;
        border-radius: 1px;
        line-height: 16px;
        font-size: 12px;
        color: rgb(77, 85, 93);
    }
    .block.active{
        color: #fff;
    }
    .positive{
        background: rgba(0, 160, 220, .2);
    }
    .positive.active{
        background: rgb(0, 160, 220);
    }
    .negative{
        background: rgba(77, 85, 93, .2);
    }
    .negative.active{
        background: rgb(77, 85, 93);

    }
    .ratings-count{
        margin-left: 2px;
        font-size: 8px;
    }
.switch{
    padding: 12px 18px;
    line-height: 24px;
    border-bottom: 1px solid rgba(7, 17, 27, .1);
    color: rgb(147, 153, 159);
    font-size: 0;
}
.switch-icon{
    display: inline-block;
    width: 24px;
    height: 24px;
    margin-right: 4px;
    background-image: url('check_circle.png');
    background-size: 24px 24px;
    background-position: center;
    background-repeat: no-repeat;
}
.switch-icon.on{
    background-image: url('check_circle_on.png');
}
.switch-text{
    font-size: 12px;
    vertical-align: top;
}
</style>

