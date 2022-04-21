<template>
  <div id="app">
    <Calendar
      :events="events"
    />
  </div>
</template>

<script>
import Calendar from '@/components/Calendar'

export default {
  name: 'App',
  components: {
    Calendar
  },
  data () {
    return {
      events: [
        {
          id: 1,
          date: '2022-04-21',
          type: 'success',
          text: 'Коммуникационный дизайн среды'
        },
        {
          id: 2,
          date: '2022-04-05',
          type: 'warning',
          text: 'Коммуникационный дизайн среды'
        },
        {
          id: 3,
          date: '2022-04-18',
          type: 'error',
          text: 'Коммуникационный дизайн среды'
        }
      ]
    }
  }
}
</script>
