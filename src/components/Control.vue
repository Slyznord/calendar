<template>
  <div class="calendar__control">
    <ChevronLeftIcon
      class="calendar__control-item"
      @click="$emit('change-month', -1)"
    />

    <p class="text-xl font-semibold text-blue-900 uppercase mx-4 w-24 text-center select-none">
      {{ currentMonth }}
    </p>

    <p
      v-show="currentYear"
      class="text-xl font-semibold text-blue-900 uppercase w-24 text-center select-none"
    >
      {{ currentYear }}
    </p>

    <ChevronRightIcon
      class="calendar__control-item"
      @click="$emit('change-month', 1)"
    />
  </div>
</template>

<script>
import { ChevronLeftIcon, ChevronRightIcon } from '@vue-hero-icons/outline'

export default {
  name: 'Control',
  components: {
    ChevronLeftIcon,
    ChevronRightIcon
  },
  props: {
    currentMonth: {
      type: String,
      default: ''
    },
    currentYear: {
      type: Number,
      default: 0
    }
  }
}
</script>
