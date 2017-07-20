<template>
  <transition name="move">
    <div v-show="showFlag" class="food" ref="food">
      <div class="food-content">
        <div class="img-header">
          <img :src="food.image">
          <div class="back" @click="hide">
            <i class="icon-arrow_lift"></i>
          </div>
        </div>
        <div class="content">
          <h1 class="title">{{food.name}}</h1>
          <div class="detail">
            <span class="sell-count">月售{{food.sellCount}}份</span>
            <span class="rating">好评率{{food.rating}}%</span>
          </div>
          <div class="price">
            <span class="now">¥{{food.price}}</span><span class="old" v-show="food.oldPrice">¥{{food.oldPrice}}</span>
          </div>
          <div class="cartcontrol-wrapper">
            <cartControl :food="food" @add="addFood"></cartControl>
          </div>
          <transition name="fade">
            <div class="clickBuy" v-show="!food.count||food.count===0" @click.stop.prevent="addCart">加入购物车</div>
          </transition>
        </div>
        <split v-show="food.info"></split>
        <div class="info" v-show="food.info">
          <h1 class="title">商品信息</h1>
          <p class="text">{{food.info}}</p>
        </div>
        <split></split>
        <div class="rating">
          <h1 class="title">商品评价</h1>
          <ratingSelect @select="select" @toggle="switchOn" :selectType="selectType" :onlyContent="onlyContent" :desc="desc" :ratings="food.ratings"></ratingSelect>
          <div class="rating-wrapper">
            <ul v-show="food.ratings && food.ratings.length">
              <li v-for="rating in food.ratings" class="rating-items" v-show="needShow(rating.rateType,rating.text)">
                <div class="user">
                  <span class="name">{{rating.username}}</span>
                  <img :src="rating.avatar"  class="avatar" width="12" height="12">
                </div>
                <div class="time">{{rating.rateTime | formatDate}}</div>
                <p class="text">
                  <i :class="{'icon-thumb_down':rating.rateType===1,'icon-thumb_up':rating.rateType===0}"></i>{{rating.text}}
                </p>
              </li>
            </ul>
            <div class="no-rating" v-show="!food.ratings || food.ratings.length===0">暂无评价</div>
          </div>
        </div>
      </div>
    </div>
  </transition>
</template>

<script type='text/ecmascript-6'>
  import BScroll from 'better-scroll';
  import cartControl from '../../components/cartcontrol/cartcontrol.vue';
  import Vue from 'vue';
  import split from '../../components/split/split.vue';
  import ratingSelect from '../../components/ratingselect/ratingselect.vue';
  import {formatDate} from '../../common/js/date';

  const ALL = 2;

  export default{
    data() {
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
    props: {
      food: {
        type: Object
      }
    },
    methods: {
      needShow(type, text) {
        if (this.onlyContent && !text) {
          return false;
        }
        if (this.selectType === ALL) {
          return true;
        } else {
          return type === this.selectType;
        }
      },
      switchOn() {
        this.onlyContent = !this.onlyContent;
        this.$nextTick(() => {
          this.scroll.refresh();
        });
      },
      select(type) {
        this.selectType = type;
        this.$nextTick(() => {
          this.scroll.refresh();
        });
      },
      show() {
        this.showFlag = true;
        this.selectType = ALL;
        this.onlyContent = false;
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
      hide() {
        this.showFlag = false;
      },
      addCart(event) {
        this.$emit('add', event.target);
        Vue.set(this.food, 'count', 1);
      },
      addFood(target) {
        this.$emit('add', target);
      }
    },
    components: {
      cartControl,
      split,
      ratingSelect
    },
    filters: {
      formatDate(time) {
        let date = new Date(time);
        return formatDate(date, 'yyyy-MM-dd hh:mm');
      }
    }
  };
</script>

<style lang="scss" rel="stylesheet/scss">
  @import "../../common/sass/mixin";

  .food{
    position: fixed;
    top: 0;
    left: 0;
    bottom: 48px;
    z-index: 30;
    width: 100%;
    background: #fff;
    &.move-enter-active,&.move-leave-active {
      transition: all 0.2s linear;
    }
    &.move-enter,&.move-leave-to {
      transform: translate3d(100%,0,0);
    }
    .img-header{
      position: relative;
      width: 100%;
      height: 0;
      padding-top: 100%;
      img{
        position: absolute;
        top: 0;
        left: 0;
        width: 100%;
        height: 100%;
      }
      .back{
        position: fixed;
        top: 7%;
        left: 4%;
        .icon-arrow_lift{
          display: block;
          padding: 8px;
          font-size: 20px;
          color: #ddd;
        }
      }
    }
    .content{
      padding: 18px;
      position: relative;
      .title{
        margin-bottom: 8px;
        line-height: 14px;
        font-size:14px;
        font-weight: 700;
        color: rgb(7,17,27);
      }
      .detail{
        margin-bottom: 18px;
        font-size: 0;
        color: rgb(147, 153, 159);
        line-height: 10px;
        height: 10px;
        .sell-count,.rating{
          font-size: 10px;
        }
        .sell-count{
          margin-right: 12px;
        }
      }
      .price{
        font-weight: 700;
        line-height: 24px;
        .now{
          margin-right: 8px;
          font-size: 14px;
          color: rgb(240,20,20);
        }
        .old{
          text-decoration: line-through;
          font-size: 10px;
          color: rgb(147,153,159);
        }
      }
      .cartcontrol-wrapper{
        position: absolute;
        right: 12px;
        bottom: 12px;
      }
      .clickBuy{
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
        background: rgb(0, 160, 220);
        &.fade-enter-active,&.fade-leave-active{
          transition: all 0.2s;
        }
        &.fade-enter,&.fade-leave-to{
          opacity: 0;
        }
      }
    }
    .info{
      padding: 18px;
      .title{
        line-height:14px;
        margin-bottom: 16px;
        font-size:14px;
        font-weight: 700;
        color: rgb(7,17,27);
      }
      .text{
        line-height:24px;
        font-weight: 200;
        font-size:12px;
        padding: 0 8px;
        color: rgb(77,85,93)
      }
    }
    .rating{
      padding-top: 18px;
      .title{
        line-height:14px;
        margin-left: 18px;
        font-size:14px;
        font-weight: 700;
        color: rgb(7,17,27);
      }
      .rating-wrapper{
        padding: 0 18px;
        .rating-items{
          position: relative;
          padding: 16px 0;
          @include border-1px(rgba(7,17,27,0.1));
          .user{
            position: absolute;
            right: 0;
            top: 16px;
            line-height: 12px;
            font-size: 0;
            .name{
              display: inline-block;
              vertical-align: top;
              margin-right: 6px;
              font-size:10px;
              color: rgb(147,153,159);
            }
            .avatar{
              border-radius: 50%;
              display: inline-block;
            }
          }
          .time{
            margin-bottom: 6px;
            font-size:10px;
            line-height:12px;
            color: rgb(147,153,159);
          }
          .text{
            font-size:12px;
            line-height: 16px;
            color: rgb(7,17,27);
            .icon-thumb_down,.icon-thumb_up{
              margin-right: 4px;
            }
            .icon-thumb_down{
              color: rgb(147,153,159);
            }
            .icon-thumb_up{
              color: rgb(0, 160, 220);
            }
          }
        }
        .no-rating{
          padding: 16px 0;
          font-size:12px;
          color: rgb(147,153,159);
        }
      }
    }
  }
</style>
