<template lang="pug">
.calendar(@touchstart="handleStart" @touchmove="handleMove" @touchend="handleEnd")
  .calendar-header
    .calendar-header-item
      span {{ date.getFullYear() }}
      span -
      span {{ date.getMonth() + 1 }}
  .calendar-week
    .calendar-week-item(
      v-for="item in weekList"
      :key="item.value"
    ) {{ item.label }}
  .calendar-body
    .calendar-content(:style="contentStyle")
      .calendar-card(
        v-for="(card, key) in cardMap"
      )
        .calendar-item(
          v-for="item in card"
        ) {{ item.value }}
  //- button(@click="setPrevMonthDayList") 上一页
  //- button(@click="setNextMonthDayList") 下一页
</template>
<script>

let windowWidth = window.innerWidth
if (windowWidth > 700) windowWidth = 700

const util = {
  // 根据日期获取这个月份第一天是星期几
  getFirstDayWeekByDate (year, month) {
    let d = new Date(year, month, 1)
    return d.getDay()
  },
  // 根据日期获取这个月份一共有多少天 (20 ~ 31)
  getTotalDayByDate (year, month) {
    let d = new Date(year, month + 1, 0)
    return d.getDate()
  },
  // 根据范围返回多少天的列表
  getListByRange (start, end, type) {
    let list = []
    for (let i = start; i <= end; i++) {
      let temp = {
        value: i,
        type
      }
      list.push(temp)
    }
    return list
  },
  createDayByDate (date) {
    let year = date.getFullYear() // 获取月份
    let month = date.getMonth() // 获取年份
    let firstDayWeek = this.getFirstDayWeekByDate(year, month) // 获取月份的第一天是星期几
    firstDayWeek === 0 && (firstDayWeek = 7) // 因为 星期天 = 0 所以星期天要变成 7
    let totalDay = this.getTotalDayByDate(year, month) // 获取月份总共有多少天
    let prevMonthTotalDay = this.getTotalDayByDate(year, month - 1) // 获取上个月份总共有多少天
    let prevStartDay = prevMonthTotalDay - (firstDayWeek - 1) // 获取上个月的开始天数
    let list = []

    // 获取 上个月 这个月 下个月 的天数列表
    let prevMonthDayList = this.getListByRange(prevStartDay + 1, prevMonthTotalDay, 'prev')
    let currMonthDayList = this.getListByRange(1, totalDay, 'curr')
    let nextMonthDayList = this.getListByRange(1, 42 - totalDay - (prevMonthTotalDay - prevStartDay), 'next')
    // 添加进列表
    list.push(...prevMonthDayList, ...currMonthDayList, ...nextMonthDayList)
    return list
  },
  changeListByType (list, type) {
    return list.map(item => {
      return {
        value: item.value,
        type: item.type
      }
    })
  }
}

export default {
  data () {
    return {
      date: new Date(),
      weekList: [
        { value: 1, label: '一' },
        { value: 2, label: '二' },
        { value: 3, label: '三' },
        { value: 4, label: '四' },
        { value: 5, label: '五' },
        { value: 6, label: '六' },
        { value: 7, label: '日' }
      ],
      cardMap: {
        prevList: [],
        currList: [],
        nextList: []
      },
      isMove: false,
      left: windowWidth
    }
  },
  mounted () {
    this.init()
  },
  computed: {
    contentStyle () {
      let style = {
        width: windowWidth * 3 + 'px',
        transform: `translate3d(${-this.left}px, 0, 0)`
      }
      if (!this.isMove) {
        style.transition = '300ms'
      }
      return style
    }
  },
  methods: {
    init () {
      let date = this.date
      let month = date.getMonth()
      let prevDate = new Date(date)
      prevDate.setMonth(month - 1)
      let nextDate = new Date(date)
      nextDate.setMonth(month + 1)
      this.cardMap.prevList = util.createDayByDate(prevDate)
      this.cardMap.currList = util.createDayByDate(date)
      this.cardMap.nextList = util.createDayByDate(nextDate)
    },
    // toPrevCard () {
    //   let nextDate = new Date(this.date)
    //   nextDate.setMonth(nextDate.getMonth() - 1)
    //   this.date.setMonth(this.date.getMonth() + 1)
    //   this.cardMap.nextList = util.createDayByDate(nextDate)
    //   this.cardMap.currList = util.changeListByType(this.cardMap.currList, 'next')
    //   this.cardMap.currList = util.changeListByType(this.cardMap.prevList, 'curr')
    // },
    toNextCard () {
      let prevDate = new Date(this.date)
      prevDate.setMonth(prevDate.getMonth() - 1)
      this.date.setMonth(this.date.getMonth() + 1)
      this.cardMap.nextList = util.changeListByType(this.cardMap.currList)
      this.cardMap.currList = util.changeListByType(this.cardMap.prevList)
      this.cardMap.prevList = util.createDayByDate(prevDate)
    },
    handleStart (event) {
      let e = event.touches[0]
      this.startDate = new Date()
      this.pageX = e.pageX
      this.startLeft = this.left
      this.isMove = true
    },
    handleMove (event) {
      if (!this.isMove) return
      let e = event.touches[0]
      let left = this.left - (e.pageX - this.pageX)
      if (left < 0) left = 0 // 判断是否超出左边界
      if (left > windowWidth * 2) left = windowWidth * 2 // 判断是否超出右边界
      this.left = left
      this.pageX = e.pageX
    },
    handleEnd (e) {
      this.isMove = false
      // let diffDate = new Date() - this.startDate // 本次移动的时间差
      // let diffLeft = Math.abs(this.left - this.startLeft) // 本次移动的距离差
      let remainder = this.left % windowWidth
      if (remainder < windowWidth / 2) {
        this.left -= remainder
        // 移动到左侧
      } else {
        // 移动到右侧
        this.left -= remainder
        this.left += windowWidth
        setTimeout(() => {
          this.toNextCard()
        }, 300)
      }
    }
  }
}
</script>

<style lang="less">
body {
  margin: 0;
}
.calendar {
  max-width: 700px;
  user-select: none;
  .calendar-header {
    background-color: #ddd;
  }
  .calendar-body,
  .calendar-week {
    box-sizing: border-box;
    overflow: hidden;
    display: flex;
    flex-wrap: wrap;
  }
  .calendar-item,
  .calendar-week-item {
    flex: 1;
    width: 100% / 7;
    border-bottom: 1px solid #eee;
    float: left;
    display: flex;
    align-items: center;
    justify-content: center;
    box-sizing: border-box;
    position: relative;
    &::after {
      content: '';
      margin-top: 100%;
    }
  }
  .calendar-item {
    // cursor: pointer;
    &:hover {
      background-color: #f5f5f5;
    }
    &.active {
      color: #fff;
      background-color: #f06;
    }
  }
  .calendar-card {
    width: 100%;
    box-shadow: 0 0 5px #ddd;
    flex-shrink: 0;
    overflow: hidden;
  }
  .calendar-content {
    display: flex;
    flex-wrap: nowrap;
  }
}
</style>
