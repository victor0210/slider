<template>
  <div class="demo-slider">
    <div class="demo-slider__under-line"
      @click="jumpAhead"
    >
      <div class="demo-slider__cover-line"
         :style="{
           width: coverPercent
         }"
      ></div>
    </div>
    <div class="demo-slider__trigger"
      :style="{
        left: coverPercent
      }"
      @mousedown="startMoving"
    ></div>
  </div>
</template>

<script>
export default {
  props: {
    width: String,
    fillColor: String,
    underColor: String,
    min: {
      type: Number,
      default: 0
    },
    max: {
      type: Number,
      default: 100
    },
    unit: {
      type: Number,
      default: 1
    },
    value: {
      type: Number,
      default: 10
    }
  },

  methods: {
    setValue (val) {
      if (val > this.max) {
        this.$emit('input', this.max)
      } else if (val < this.min) {
        this.$emit('input', this.min)
      } else {
        this.$emit('input', val)
      }

      //我们先将方法抽象出来，具体实现请继续往下看
      //注：Vue中的组件属性不允许被直接赋值改变，因为破坏了单项数据流，这里只是为了体现逻辑实现，后面会讲解双向绑定实现并修改此处代码
    },

    jumpAhead (e) {
      this.endX = e.x
      let percentage = (this.endX - this.$el.getBoundingClientRect().left) / this.$el.offsetWidth
      let val = Math.floor((percentage + (this.min + this.unit / 2) / (this.max - this.min)) * (this.max - this.min) / this.unit) * this.unit

      this.setValue(val)
    },

    startMoving () {
      this.addMovingListener()
    },

    stopMoving () {
      this.removeMovingListener()
    },

    addMovingListener () {
      window.addEventListener('mousemove', this.jumpAhead)
      window.addEventListener('mouseup', this.stopMoving)
    },
    removeMovingListener () {
      window.removeEventListener('mousemove', this.jumpAhead)
      window.removeEventListener('mouseup', this.stopMoving)
    }
  },

  computed: {
    coverPercent () {
      return `${100 * (this.value - this.min) / (this.max - this.min)}%`
    }
  }
}
</script>

<style scoped>
  .demo-slider {
    display: inline-block;
    width: 100%;
    height: 40px;
    position: relative;
  }

  .demo-slider__under-line {
    position: absolute;
    display: inline-block;
    width: 100%;
    height: 6px;
    background-color: #eee;
    border-radius: 20px;
    overflow: hidden;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    margin: auto;
  }

  .demo-slider__cover-line {
    height: 100%;
    display: inline-block;
    background-color: #4285f4;
    float: left;
  }

  .demo-slider__trigger {
    position: absolute;
    top: 0;
    bottom: 0;
    margin: auto;
    left: 0;
    display: inline-block;
    width: 16px;
    height: 16px;
    margin-left: -8px;
    background-color: #d9e7fd;
    border-radius: 50%;
  }
</style>
