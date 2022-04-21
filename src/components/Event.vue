<template>
  <div
    :class="[
      'event',
      eventTypes[type]
    ]"
  >
    {{ getTime }} {{ value }}
  </div>
</template>

<script>
export default {
  name: 'Event',
  props: {
    value: {
      type: [String, Number],
      default: ''
    },
    type: {
      type: String,
      default: 'success'
    },
    date: {
      type: String,
      default: ''
    }
  },
  data () {
    return {
      eventTypes: {
        success: 'event_success',
        warning: 'event_warning',
        error: 'event_error'
      }
    }
  },
  computed: {
    getTime () {
      const date = new Date(this.date)
      let hours = date.getHours()
      let minutes = date.getMinutes()

      hours = hours.toString().length > 1 ? hours : `0${hours}`
      minutes = minutes.toString().length > 1 ? minutes : `0${minutes}`

      return `${hours}:${minutes}`
    }
  }
}
</script>

<style scoped>

</style>
