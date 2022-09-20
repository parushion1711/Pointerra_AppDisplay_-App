<template>
  <input v-model='inputValue'/>
</template>

<script>
export default {
  name: 'InputBox',
  props: ['modelValue'],
  data () {
    return {

    }
  },
  computed: {
    inputValue: {
      get () {
        return this.modelValue
      },
      set (value) {
        this.$emit('update:modelValue', value)
      }
    }
  }
}
</script>
