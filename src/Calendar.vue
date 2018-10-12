<template lang="pug">
.calendar
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
    .calendar-item(
      v-for="item in list"
    ) {{ item.value }}
  button(@click="setPrevMonthDayList") 上一页
  button(@click="setNextMonthDayList") 下一页
</template>
<script>

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
      list: []
    }
  },
  mounted () {
    this.setCurrMonthDayList()
  },
  methods: {
    setCurrMonthDayList () {
      this.list = util.createDayByDate(this.date)
    },
    setPrevMonthDayList () {
      this.date.setMonth(this.date.getMonth() - 1)
      this.list = util.createDayByDate(this.date)
    },
    setNextMonthDayList () {
      this.date.setMonth(this.date.getMonth() + 1)
      this.list = util.createDayByDate(this.date)
    }
  }
}
</script>

<style lang="less">
.calendar {
  max-width: 701px;
  user-select: none;
  .calendar-header {
    background-color: #ddd;
  }
  .calendar-body,
  .calendar-week {
    border-top: 1px solid #ddd;
    border-left: 1px solid #ddd;
    box-sizing: border-box;
    overflow: hidden;
  }
  .calendar-item,
  .calendar-week-item {
    width: 100px;
    height: 100px;
    border-right: 1px solid #eee;
    border-bottom: 1px solid #eee;
    float: left;
    display: flex;
    align-items: center;
    justify-content: center;
    box-sizing: border-box;
  }
  .calendar-item {
    cursor: pointer;
    &:hover {
      background-color: #f5f5f5;
    }
    &.active {
      color: #fff;
      background-color: #f06;
    }
  }
}
</style>
