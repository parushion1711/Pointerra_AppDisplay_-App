<template>
  <textarea type="text" placeholder="Description" v-model="message"></textarea>
  <DisplayBox :message="message" style="white-space: pre-wrap">
  </DisplayBox>
</template>

<script>
// import InputBox from '@/components/InputBox.vue'
import DisplayBox from '@/components/DisplayBox.vue'

export default {
  name: 'FeatureView',
  components: {
    DisplayBox
  },
  data () {
    return {
      message: ''
    }
  }
}

</script>
