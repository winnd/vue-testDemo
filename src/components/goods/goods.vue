<template>
  <div>
    <div class="goods">
      <div class="menu-wrapper" ref="menu_wrapper">
        <ul>
          <li v-for="item in goods" class="menu-item">
          <span class="text border-1px">
            <span v-show="item.type>0" class="icon" :class="classMap[item.type]"></span>{{item.name}}
          </span>
          </li>
        </ul>
      </div>
      <div class="foods-wrapper" ref="foods_wrapper">
        <ul>
          <li @click="selectFood(food,$event)" v-for="item in goods" class="food-list">
            <h1 class="title">{{item.name}}</h1>
            <ul>
              <li v-for="food in item.foods" class="food-item">
                <div class="icon">
                  <img width="57" height="57" :src="food.icon">
                </div>
                <div class="content">
                  <h2 class="name">{{food.name}}</h2>
                  <p class="desc">{{food.description}}</p>
                  <div class="extra">
                    <span class="count">月售{{food.sellCount}}份</span><span>好评率{{food.rating}}%</span>
                  </div>
                  <div class="price">
                    <span class="now">&yen;{{food.price}}</span>
                    <span v-show="food.oldPrice" class="old">&yen;{{food.oldPrice}}</span>
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
      <shopcart :delivery-price="seller.deliveryPrice" :min-price="seller.minPrice"></shopcart>
    </div>
    <food :food="selectedFood" v-ref:food></food>
  </div>
</template>

<script type="text/ecmascript-6">
  import Axios from 'axios';
  import BScroll from 'better-scroll';
  import cartcontrol from '../cartcontrol/cartcontrol';
  import shopcart from '../shopcart/shopcart';
  import food from '../food/food';

  const ERR_OK = 0;

  export default{
    props: {
      seller: {
        type: Object
      }
    },
    data () {
      return {
        goods: [],
//        listHeight:[],
//        scrollY:0,
        selectdFood: {}
      };
    },
    created () {
      Axios.get('/api/goods').then((res) => {
        res = res.data;
        if (res.errno === ERR_OK) {
          this.goods = res.data;
          this.$nextTick(() => {
            this._initScroll();
          });
        }
      });
      this.classMap = ['decrease', 'decrease', 'gurarantee', 'invoice', 'special'];
    },
    methods: {
//      selectMenu(index, event){
//        if(!event._constructed){
//            return;
//    }
// }
      selectFood(food, event) {
        if (!event._constructed) {
          return;
        }
        this.selectdFood = food;
        this.$ref.food.show();      // 调用子组件的方法
      },
      _initScroll () {
        this.menuScroll = new BScroll(this.$refs.menu_wrapper, {
          click: true
        });
        this.foodsScroll = new BScroll(this.$refs.foods_wrapper, {
          click: true
        });
      }
    },
    components: {
      shopcart,
      cartcontrol,
      food
    }
  };
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import '../../common/stylus/mixin.styl'

  .goods
    display: flex
    position: absolute
    top: 174px
    bottom: 46px
    width: 100%
    overflow: hidden
    .menu-wrapper
      flex: 0 0 80px
      width: 80px
      background: #f3f5f7
      .menu-item
        display: table
        height: 54px
        width: 56px
        padding: 0 12px
        line-height: 14px
        .icon
          display inline-block
          vertical-align: top
          width: 12px
          height: 12px
          margin-right: 2px
          background-size: 12px 12px
          background-repeat: no-repeat
          &.decrease
            bg-image('decrease_3')
          &.discount
            bg-image('discount_3')
          &.gurarantee
            bg-image('guarantee_3')
          &.invoice
            bg-image('invoice_3')
          &.special
            bg-image('special_3')
        .text
          display: table-cell
          width: 56px
          vertical-align: middle
          border-1px(rgba(7, 17, 27, 0.1))
          font-size: 12px
    .foods-wrapper
      flex: 1
      .title
        padding-left: 14px;
        height: 26px
        line-height: 26px
        border-left: 2px solid #d9dde1
        font-size: 12px
        color: rgb(147, 153, 159)
        background: #f3f5f7
      .food-item
        display: flex
        margin: 18px
        padding-bottom: 18px
        border-1px(rgba(7, 17, 27, 0.1))
        &:last-child
          border-none()
          margin-bottom: 0
        .icon
          flex: 0 0 57px
          margin-right: 10px
        .content
          flex: 1
          .name
            margin: 2px 0 8px 0
            height: 14px
            line-heihgt: 14px
            font-size: 14 p
            color: rgb(7, 17, 27)
          .desc, .extra
            line-height: 10px
            font-size: 10px
            color: rgb(147, 153, 159)
          .desc
            line-height: 12px
            margin-bottom: 8px
          .extra
            .count
              margin-right: 12px
          .price
            font-weight: 700
            line-height: 24px
            .now
              margin-right: 8px
              font-size: 14px
              color: rgb(240, 20, 20)
            .old
              text-decoration: line-through
              font-size: 10px
              color: rgb(147, 153, 159)
          .cartcontrol-wrapper
            position: absolute
            right: 0
            bottom: 12px
</style>
