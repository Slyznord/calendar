<template>
  <div
    :class="[
      'cell',
      classes
    ]"
  >
    {{ value }}
    <slot name="event" />
  </div>
</template>

<script>
export default {
  name: 'Cell',
  props: {
    classes: {
      type: String,
      default: ''
    },
    value: {
      type: [Number, String],
      required: true
    }
  }
}
</script>
