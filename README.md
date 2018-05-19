#分享一些前端组件开发思路，附demo

其实不管是什么框架，在开发中都有一些比较常规和通用的东西，那就是思想，接下来我将从以下三个点并基于vue开发一个Slider（滑条）组件，在这里我默认大家都会基本使用vue，若对vue相关概念还不是很清楚的可以转到 [Vue.js](https://cn.vuejs.org/v2/guide)

## 概览
### 步骤
1. 组件抽象 (api设计)
2. 页面实现 (组件的html实现和css样式）
3. 建立关联 (组装)

### 项目构建
这里我们使用[TUI-CLI](https://github.com/Bennnis/tui-cli)快速搭建构建项目
我们只需要关心src下的App.vue和components目录即可

- src
  - App.vue
  - components/

##1.组件抽象
首先在开发之前，我们需要对组件的逻辑进行一个抽象操作，不需要关心页面如何去实现。

如何去抽象？很简单，两个点，属性和方法

比如我们现在抽象一个人：

1. 属性：身高，体重，爱好，头发颜色，脚的尺寸等等
2. 方法：吃饭，说话，打游戏，敲代码等等

我们再来抽象一个灯泡：

1. 属性：亮度，灯的颜色，灯的形状（圆的，细长的），灯的耗能等待
2. 方法：开，关 （当然这里还可以抽象成一个方法，就是“切换”）

抽象的程度决定了代码的复杂度和拓展性

好了言归正传，现在我们要抽象一个滑条组件需要怎么做呢？按我们上面的顺序来试试，在这里我们能想到多少就写多少，功能设计上有考虑不足是很正常的，因为我确实就没想到啊！不过没关系，我们先试试

1. 属性：宽度，填充色，打底色，最大值，最小值，单位值，当前选中值
2. 方法：改变值方法

好了，我现在就想到这些，先试试看呢！在设计api的时候也要考虑好属性的类型和初始值，比如这里这里的min和max还有value，我们严格要求成数字，并默认min为0，max为100，value为0

ps：如果用户传入了对应的属性值，那default将会被覆盖

在components目录下新建一个 slider.vue 文件，并添加以下代码

```javascript
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
      default: 0
    }
  },

  methods: {
    setValue (val) {
      //我们先将方法抽象出来，具体实现请继续往下看
      //注：Vue中的组件属性不允许被直接赋值改变，因为破坏了单项数据流，这里只是为了体现逻辑实现，后面会讲解双向绑定实现并修改此处代码
    }
  }
}

```

## 2.页面实现
页面实现实际上去实现页面元素的布局，当然在写代码之前我们也可以做一下简单的页面元素的抽象。
一个滑条由什么元素组成呢？

1. 一个类进度条（拥有背景元素和填充元素）
2. 一个可左右拖动的触发元素。

话不多说上代码

```html

<div class="t-slider">
  <div class="t-slider__under-line">
    <div class="t-slider__cover-line"></div>
  </div>
  <div class="t-slider__trigger"></div>
</div>

<style>
  .t-slider {
    display: inline-block;
    width: 100%;
    height: 40px;
    position: relative;
  }
  
  .t-slider__under-line {
    position: absolute;
    display: inline-block;
    width: 100%;
    height: 6px;
    background-color: $--border-color-base;
    border-radius: 20px;
    overflow: hidden;
    top: 0;
    bottom: 0;
    left: 0;
    right: 0;
    margin: auto;
  }

  .t-slider__cover-line {
    height: 100%;
    display: inline-block;
    background-color: $--color-primary;
    float: left;
  }

  .t-slider__trigger {
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

```

至此，我们的滑条组件的页面基本构造已经完成了。接下来我们将组件注册并在页面使用即可，看看接下来我们会遇到什么问题。

我们在App.vue引入slider组件或者使用Vue.component()全局注册，具体用法请参照Vue官方文档

修改App.vue为下列内容 （本例结合TUI组件库的部分组件进行页面的快速布局，TUI-CLI自动安装好了TUI，想了解更多请见[TUI](https://github.com/Bennnis/TUI), 喜欢的话顺便给个 star 哦！)

```script
<template>
  <div id="app">
    <t-row>
      <t-col :span="8" :offset="8">
        <slider-demo/>
      </t-col>
    </t-row>
  </div>
</template>

<script>
import SliderDemo from './components/slider.vue'
export default {
  components: {
    SliderDemo
  }
}
</script>
```

## 3.组装
我们在页面上注册并使用了 <slider-demo/> 组件，接下来我们需要做什么呢？既然是输入组件，那双向绑定自然是少不了的。

ps: 

* outM: 组件外部Model
* outV: 组件外部View
* inM: 组件内部Model
* inV: 组件内部View

对于这种输入组件我的开发思路一般是：

1. outM ↔ inM
2. outM ↔ outV， inM ↔ inV

### 1.outM ↔ inM
实现第一步，组件外部Model->组件内部Model，修改App.vue

```html
<t-slider v-model="userInput"/>

*
* 省略部分
*

export default {
  *
  * 省略部分
  *
  
  data: {
    return {
      userInput: 0
    }
  }
}
```

再实现第二步，组件内部Model->组件外部Model, 修改slider.vue，这里并没有涉及到view的操作，我们下面会继续分析

```html
*
*  省略部分
*
setValue (val) {
  this.$emit('input', val)	
}
```

是不是很简单？outM ↔ inM实现完成。

那接下来我们继续拓展

outM ↔ inM => outV ↔ outM, inM ↔ inV

其实能走到这里后面的开发问题都不大了，只需要Model和View建立关联即可

我们从 inM ↔ inV 开始

###2. M ↔ V
####1. inM -> inV
我们首先要靠考虑我们需要在视图上怎么去展现，不同于input组件，slider组件的展现形式更像是一个进度条，那我们肯定就要通过一些特殊操作了。

数据流向：M -> width -> V （通过value计算出一个width相关值并在视图上去设置样式）

我们修改一下slider.vue中的 "demo-slider__cover-line",并添加计算 “进度” 的属性coverPercent

```html
<div class="demo-slider__cover-line"
 :style="{
   width: coverPercent
 }"
></div>

*
* 省略部分
*

computed: {
  coverPercent () {
    return `${100 * (this.value - this.min) / (this.max - this.min)}%`
  }
}

```
打开看看，好像漏了点什么，触发按钮好像也需要根据值的改变而改变位置，我们再修改一下"demo-slider__trigger"

```html
<div class="demo-slider__trigger"
  :style="{
    left: coverPercent
  }"
></div>
```

庆祝一下，inM -> inV 完成，您可以手动改变value的绑定值来看一下效果

###2. inV -> inM

对于slider组件来说，比较普遍的操作当然就是拖动触发按钮改变值了，或者直接点击under-line的某个位置改变值。这里我们从 "点击改变" 开始讲。

```html
// 添加点击事件
<div class="demo-slider__under-line" @click="jumpAhead"> *** </div>

*
* 省略部分
*

methods: {
  *
  * setValue method
  * 
  
  jumpAhead (e) {
    this.endX = e.x
    let percentage = (this.endX - this.$el.getBoundingClientRect().left) / this.$el.offsetWidth
    let val = Math.floor((percentage + (this.min + this.unit / 2) / (this.max - this.min)) * (this.max - this.min) / this.unit) * this.unit

    this.setValue(val)
  }
}
```

点击操作就已经完成了，我们的 inV -> inM 也拼接好了，其实到这里，咱们的组件也开发得差不多了，最后我们一起来实现一下拖动按钮改变值。原理大概分为以下几步

1. 鼠标按下（触发按钮）：mousedown
2. 鼠标移动（任意位置）：mousemove, 这里我们可以把每一次细微的改变都看成一次直接跳转，只是距离足够小，改变足够快，看起来就像在滑动，直接复用jumpAhead方法。但是这里有个问题，因为鼠标移动可能在页面的任何地方，所以需要在window上添加mousemove的监听，并在鼠标放开时移除即可
3. 鼠标放开（任意位置）：mouseup

直接贴代码了

```html
<div class="demo-slider__trigger"
  :style="{
    left: coverPercent
  }"
  @mousedown="startMoving"
></div>

*
* 省略部分
*

methods: {
  *
  * 省略部分
  *
  
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
}
```

拖动的时候可能会超出宽度范围，没关系，我们在setValue方法上限制一下即可

```html
setValue (val) {
  if (val > this.max) {
    this.$emit('input', this.max)
  } else if (val < this.min) {
    this.$emit('input', this.min)
  } else {
    this.$emit('input', val)
  }
}

```

至此，我们的slider组件就开发完成了，大家也可以试试传入其他值不仅仅是value。对了，上面还有几个属性没有组装哦，小伙伴们可以尝试一下，接着我的demo继续往下做，看能不能开发出更实用或者更有趣的功能。

源码已上传至[Github](https://github.com/Bennnis/slider)

再次贴上个人开发的组件库 [TUI](https://github.com/Bennnis/TUI)（您的star和issue是对我最大的支持）

非常感谢您能看完这篇文章！
