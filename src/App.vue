<template>
  <div id="app">
    <t-row>
      <t-col :span="8" :offset="8">
        <slider-demo v-model="userInput" :unit="10" :max="50" :min="-10"/>
        {{ userInput }}
      </t-col>
    </t-row>
  </div>
</template>

<script>
import SliderDemo from './components/slider.vue'
export default {
  components: {
    SliderDemo
  },
  data () {
    return {
      userInput: 10
    }
  }
}
</script>
