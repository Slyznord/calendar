<template>
  <div class="calendar">
    <Control
      :current-month="getCurrentMonth"
      :current-year="isCurrentYear"
      @change-month="changeMonth($event)"
    />

    <header class="calendar__header">
      <div
        v-for="(item, index) in getDaysOfWeek"
        :key="index"
        class="calendar__day"
      >
        {{ item }}
      </div>
    </header>

    <div class="calendar__days">
      <Cell
        v-for="(item, index) in getCalendar"
        :key="index"
        :value="item.value"
        :classes="item.classes"
      >
        <template #event>
          <Event
            v-for="event in item.events"
            :key="event.id"
            :value="event.text"
            :type="event.type"
            :date="event.date"
          />
        </template>
      </Cell>
    </div>
  </div>
</template>

<script>
import Cell from '@/components/Cell'
import Control from '@/components/Control'
import Event from '@/components/Event'

import moment from 'moment'
import 'moment/locale/ru'

moment.locale('ru')

export default {
  name: 'Calendar',
  components: {
    Cell,
    Control,
    Event
  },
  props: {
    events: {
      type: Array,
      default: () => []
    }
  },
  data () {
    return {
      formatDays: 'dddd',
      currentDate: moment(),
      currentYear: moment().year(),
      currentMonth: moment().month() + 1,
      windowWidth: 0
    }
  },
  computed: {
    isCurrentYear () {
      return this.currentYear === moment().year() ? 0 : this.currentYear
    },
    getCurrentMonth () {
      return moment(`${this.currentYear}-${this.currentMonth}`, 'YYYY-MM').format('MMMM')
    },
    getDaysOfWeek () {
      return Array.apply(null, Array(7)).map((item, index) => moment(index, 'e').format(this.formatDays))
    },
    getCalendar () {
      return this.createCalendar(this.currentYear, this.currentMonth)
    }
  },
  created () {
    this.updateWidth()
    window.addEventListener('resize', this.updateWidth)
  },
  beforeDestroy () {
    window.removeEventListener('resize', this.updateWidth)
  },
  methods: {
    getClasses (targetDate, currentDay) {
      if (targetDate < this.currentDate && !this.currentDate.isSame(targetDate, 'day')) {
        return 'cell_past'
      } else if (currentDay === 6 || currentDay === 0) {
        return 'cell_weekend'
      } else if (this.currentDate.isSame(targetDate, 'year') && this.currentDate.isSame(targetDate, 'month') && this.currentDate.isSame(targetDate, 'day')) {
        return 'cell_today'
      } else {
        return 'cell_default'
      }
    },
    getEndOfPrevMonthDays (year, month, startOfMonth) {
      const targetDate = moment(`${year}-${month}`, 'YYYY-MM')
      targetDate.add(-1, 'month')
      // const targetMonth = month || 12
      // const targetYear = targetMonth === 12 ? year - 1 : year
      // const targetDate = moment(`${targetYear}-${targetMonth}`, 'YYYY-MM')
      const totalDays = targetDate.daysInMonth()
      const days = []

      startOfMonth = startOfMonth || 7

      for (let i = 1; i < startOfMonth; i++) {
        const targetDay = totalDays - startOfMonth + i + 1

        targetDate.set('date', targetDay)
        days.push({
          classes: this.getClasses(targetDate, targetDate.day()),
          events: [],
          value: targetDay
        })
      }

      return days
    },
    getCurrentMonthDays (year, month, startOfMonth) {
      const days = []

      while (startOfMonth.month() + 1 === this.currentMonth) {
        days.push({
          classes: this.getClasses(moment(startOfMonth), startOfMonth.day()),
          events: this.events.filter(item => moment(startOfMonth).isSame(moment(item.date), 'date') && moment(startOfMonth).isSame(moment(item.date), 'month') && moment(startOfMonth).isSame(moment(item.date), 'year')),
          value: startOfMonth.date()
        })

        startOfMonth.set('date', startOfMonth.date() + 1)
      }

      return days
    },
    getStartOfNextMonthDays (year, month, endOfMonth) {
      const targetDate = moment(`${year}-${month}`, 'YYYY-MM')
      targetDate.add(1, 'month')
      // В moment js 0 обозначает воскресенье, если день окончания месяца воскресенье
      // то вместо 0 присваиваем 7
      const endDay = endOfMonth.day() || 7
      const days = []

      for (let i = 1; i <= (7 - endDay); i++) {
        targetDate.set('date', i)

        days.push({
          classes: this.getClasses(targetDate, targetDate.day()),
          events: [],
          value: i
        })
      }

      return days
    },
    createCalendar (year, month) {
      const startOfMonth = moment(`${year}-${month}`, 'YYYY-MM').startOf('month')
      const endOfMonth = moment(`${year}-${month}`, 'YYYY-MM').endOf('month')

      const endOfPrevMonthDays = this.getEndOfPrevMonthDays(year, month, startOfMonth.day())
      const currentMonthDays = this.getCurrentMonthDays(year, month, startOfMonth)
      const startOfNextMonthDays = this.getStartOfNextMonthDays(year, month, endOfMonth)

      return [...endOfPrevMonthDays, ...currentMonthDays, ...startOfNextMonthDays]
    },
    changeMonth (num) {
      if (this.currentMonth === 1 && num === -1) {
        this.currentMonth = 12
        --this.currentYear

        return
      }

      if (this.currentMonth === 12 && num !== -1) {
        this.currentMonth = 1
        ++this.currentYear

        return
      }

      this.currentMonth += num
    },
    updateWidth () {
      this.windowWidth = window.innerWidth
    }
  },
  watch: {
    windowWidth: {
      immediate: true,
      handler: function (value) {
        this.formatDays = value < 768 ? 'dd' : 'dddd'
      }
    }
  }
}
</script>
