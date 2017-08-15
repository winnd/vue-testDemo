<template>
  <div class="ratingselect">
    <div class=" rating-type border-1px">
      <span @click="select(2,$event)" class="block position" :class="{'active':selectType === 2}">{{desc.all}}
        <span class="count">{{ratings.length}}</span>
      </span>
      <span @click="select(0,$event)" class="block position" :class="{'active':selectType === 0}">{{desc.positive}}
        <span class="count">
          </span>
        </span>
        <span @click="select(1,$event)" class="block negative" :class="{'active':selectType === 1}">{{desc.negative}}
          <span class="count">{{negatives.length}}</span>
        </span>
    </div>
    <div @click="toggleContent" class="switch" :class="{'on':onlyContent}">
      <span class="icon-check_circle"></span>
      <span class="text">只看有内容的评价</span>
    </div>
  </div>
</template>

<script type="text/ecmascript-6">
const POSITIVE = 0;
const NEGATIVE = 1;
const ALL = 2;

export default {
  props: {
    ratings: {
      type: Array,
      default() {
        return [];
      }
    },
    computed: {
      positives() {
        return this.ratings.filter((rating) => {
          return rating.rateType === POSITIVE;
        });
      },
      negatives() {
        return this.ratings.filter((rating) => {
          return rating.rateType === NEGATIVE;
        });
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
      default() {
        return {
          all: '全部',
          positive: '满意',
          negative: '不满意'
        };
      }
    }
  },
  methods: {
    select(type, event) {
      if (!event._constructed) {
        return;
      }
      this.selectType = type;       // 遍历高亮
      this.$dispatch('ratingtype.select', type); // 与父组件的通信 子组件告诉父组件他的变化
    },
    toggleContent(event) {
      if (!event._constructed) {
        return;
      }
      this.onlyContent = !this.onlyContent; // 来回切换
      this.$dispatch('content.toggle', this.onlyContent);
    }
  }
};
</script>

<style lang="stylus" rel="stylesheet/stylus">
  @import "../../common/stylus/mixin.styl"

  .ratingselect
    .rating-type
      padding: 180px 0
      margin:0 18px
      border-1px(rgb(7,17,27,0.1))
      .block
        display inline-block
        padding 8px 12px
        margin-rignt 8px
        border-radius 1px
        font-size 12px
        color:rgb(77,85,93)
        &.active
          color:#fff
        .count
          margin-left 2px
          font-size 8px 
        &.positive
          background rgba(77,85,93,0.2)
          &.active
            background:rgba(0,160,220,0.2)
        &.negative
          bacground:rgba(77,85,93,0.2)
          &.active
            background:rgba(0,160,220,0.2)
    .switch
      padding:12px 18px
      line-height 24px
      border-bottom 1px solid rgba(7,17,27,0.1)
      color:rgb(147,153,159)
      font-size 0
      &.on
        .icon-check_circle
          color:#00c850
      &.icon-check_circle
        margin-right 4px
        font-size 24px
      &.text
        font-size 12px
</style>

