<template>
  <span class="ayou-day-cell"
        @click.stop.prevent="handleDayClick()"
        :title="showLunar && lunarText"
        :class="{'selected': isSelected, 'passive': day.isPassive, 'in-range': isInRange, 'festival': isFestival, 'today': isToday}"
        >
    <div class="solar">
      {{day.dayMoment.date()}}
    </div>
    <div class="lunar" v-if="showLunar">
      {{lunarText}}
    </div>
  </span>
</template>
<script type="text/ecmascript-6">
  import transformer from './solar_lunar'

  export default {
    props: {
      showLunar: {
        type: Boolean,
        default: false
      },
      day: {
        type: Object
      },
      isSelected: {
        type: Boolean,
        default: false
      },
      isInRange: {
        type: Boolean,
        default: false
      }
    },
    data () {
      return {
        lunar: this.solar2lunar(this.day)
      }
    },
    watch: {
      day (val) {
        this.lunar = this.solar2lunar(val)
      }
    },
    methods: {
      handleDayClick () {
        if (this.day.isPassive) return
        this.$emit('dayClick', this.day)
      },
      solar2lunar (day) {
        return transformer.solar2lunar(day.dayMoment.year(), day.dayMoment.month() + 1, day.dayMoment.date())
      }
    },
    computed: {
      lunarText () {
        return this.lunar && (this.lunar.calendarFestivals || this.lunar.lunarFestivals || this.lunar.Term || this.lunar.IDayCn)
      },
      isFestival () {
        if (this.lunar && (this.lunar.calendarFestivals || this.lunar.lunarFestivals || this.lunar.Term)) {
          return true
        }
        return false
      },
      // TODO double check this logic for checking if date is today. Often calendar styling provides an option to mark Today's date for user context.
      isToday () {
        if (day.dayMoment.date == moment()) {
          return true
        }
        return false
      }
    }
  }
</script>
<style lang="less" rel="stylesheet/less">
  @import "./_var.less";

  .ayou-day-cell {
    padding: 2px 0;
    width: 14.28%;
    display: inline-block;
    font-size: 1rem;
    text-align: center;
    &:hover {
      cursor: pointer;
    }
    .solar {
      display: inline-block;
      width: 2.4rem;
      height: 2.4rem;
      line-height: 2.4rem;
      font-size: 1rem;
    }
    .lunar {
      font-size: 0.8rem;
      overflow: hidden;
      text-overflow: ellipsis;
      white-space: nowrap;
    }
    // Parent Classes
    &.today {
      .solar {
        text-decoration: underline;
      }
    }
    &.selected {
      .solar {
        border-radius: 50%;
        background-color: @primary;
        color: #fff;
      }
    }
    &.in-range {
      .solar {
        border-radius: 50%;
        background-color: @primary-light;
        color: #fff;
      }
    }
    &.festival {
      .lunar {
        color: @secondary;
      }
    }
    &.passive {
      .solar {
        opacity: 0.4;
      }
      .solar {
        color: @grey;
      }
    }
  }
</style>
