<template lang="pug">
.calendar
  .calendar-header
    .calendar-header-item 2018
  .calendar-week
    .calendar-week-item(
      v-for="item in weekList"
      :key="item.value"
    ) {{ item.label }}
  .calendar-body
    .calendar-item(
      v-for="item in list"
      :key="item.value"
      :class="{ active: item.checked }"
    ) {{ item.value }}
</template>
<script>
export default {
  data () {
    return {
      startDate: null,
      endDate: null,
      weekList: [
        { value: 0, label: '日' },
        { value: 1, label: '一' },
        { value: 2, label: '二' },
        { value: 3, label: '三' },
        { value: 4, label: '四' },
        { value: 5, label: '五' },
        { value: 6, label: '六' }
      ],
      list: [
        { value: 1, checked: false },
        { value: 1, checked: false },
        { value: 1, checked: false },
        { value: 1, checked: false },
        { value: 1, checked: false }
      ]
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
