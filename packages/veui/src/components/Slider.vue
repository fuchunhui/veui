<template>
<div class="veui-slider" :ui="ui">
  <div class="veui-slider-track" ref="track" @mousedown="mousedownHandler">
    <div class="veui-slider-mark" ref="mark" :style="{width: `${localRange}px`}"></div>
    <veui-overlay class="veui-slider-thumb" ref="thumb" @mousedown.native.stop=""
      :style="{
        transform: `translateX(${localRange}px)`
      }"
      v-drag:thumb.translate="{
        draggable,
        containment: this.$refs.track,
        axis: 'x',
        ready: dragReady
      }">
    </veui-overlay>
  </div>
</div>
</template>

      <!-- :style="{
        transform: `translateX(${realRange}px)`
        transform: `translateX(${localValue}px)`
      }" -->
<script>
import { drag } from '../directives'
import Overlay from './Overlay'
import { input, ui } from '../mixins'
// ui 参考progress
// input 参考 switch

export default {
  name: 'veui-slider',
  directives: { drag },
  mixins: [input, ui],
  components: {
    'veui-overlay': Overlay
  },
  props: {
    ui: String,
    value: {
      type: Number,
      default: 10
    },
    vertical: Boolean,
    min: {
      type: Number,
      default: 0
    },
    max: {
      type: Number,
      default: 100
    },
    step: {
      type: Number,
      default: 1
    }
  },
  // 评论补充
  // Boolean默认false
  // scoped slot tip实现
  // Slider 应该实现输入组件的 mixin。
  // DOM 上的状态需要感知 realDisabled / realReadonly 两个 computed 值。
  // update:value → input

  // 支持 v-model
  // 浮层显示的内容可以完全由 scoped slot 来指定，是显示值还是百分比都可以外部指定。Scoped slot 里可以预先计算一些值方便调用。
  //
  // 对于需要和外部保持同步的 prop，应当实现 .sync 修饰符必须的 $emit('update:prop', value) 逻辑
  // prop 的本地副本，命名需带 local 前缀，例如 value → localValue，须要 watch 对应 prop 变化并同步本地值
  // prop 或 data 中数据项的计算后版本（具有很接近的语义时），需有 real 前缀，例如 keys → realKeys
  data () {
    return {
      localValue: this.value,
      localRange: 0,
      thumbRange: 0,
      startRange: 0,
      draggable: true // 优化成props, 根据是否禁用来判断true or false, 禁用的时候为false
    }
  },
  created () {
    // console.log('create :', this.numRange, this.$refs)
  },
  computed: {
    valid () { // 不合法的情况，隐藏滑块 // issue评论 // 可以是逆序的，
      return this.max > this.min
    },
    numRange () {
      return this.max - this.min
    },
    realRange: {
      get () {
        return this.valueToRange(this.localValue)
      },
      set (val) {
        // this.localValue = this.rangeToValue(val)
        let a = this.rangeToValue(val)
        // a = parseInt(a, 10)
        this.localValue = a
      }
    },
    realRange1 () {
      return this.realRange + 2
    },
    realPercent () {
      return `${100 * (this.localValue - this.min) / (this.max - this.min)}%`
    }
  },
  watch: {
    value (val) {
      this.localValue = val
    }
  },
  methods: {
    setValue (value) {
      let ava = Math.round(value)
      this.$emit('update:value', value, ava)
      console.log('value: ' + value)
      console.log('realRange: ' + this.realRange)
      console.log('format1: ' + this.valueToRange(value))
      console.log('format2: ' + this.rangeToValue(this.realRange))
      console.log('-----------------------')
    },
    valueToRange (value) {
      let range = (value - this.min) / this.numRange * this.thumbRange
      return Math.max(0, Math.min(this.thumbRange, range))
    },
    rangeToValue (range) { // 传入的range应该从起点0开始算距离   // 格式的过滤，在显示tip层面操作，不影响此处
      return this.min + this.thumbRange ? (range / this.thumbRange) * this.numRange : 0
    },
    mousedownHandler (event) {
      console.log('mousedown', event)
      console.log('mousedownHandler: ' + event.offsetX)
      if (event.offsetX !== this.localRange) {
        this.localRange = event.offsetX
      }
    },
    dragReady (obj) {},
    addDragEventListener () {
      this.$on('dragstart', this.dragstart)
      this.$on('drag', this.drag)
      this.$on('dragend', this.dragend)
    },
    dragstart ({ event }) {
      this.startRange = this.localRange
      event.preventDefault()
      event.stopPropagation()
      console.log('----------------', event)
    },
    drag ({ event, distanceX, distanceY }) {
      console.log('drag: ' + distanceX)
      this.localRange = Math.max(0, Math.min(this.thumbRange, this.startRange + distanceX))
      if (this.liveDragging) {
        this.setValue(this.localValue)
      }
    },
    dragend ({ event, distanceX, distanceY }) {
      console.log('dragend: ' + distanceX)
      if (!this.liveDragging) {
        this.setValue(this.localValue)
      }
    }
  },
  mounted () {
    this.addDragEventListener()
    let track = window.getComputedStyle(this.$refs.track)
    let thumb = window.getComputedStyle(this.$refs.thumb.$el)
    this.thumbRange = this.vertical
      ? parseFloat(track.height, 10) - parseFloat(thumb.height, 10)
      : parseFloat(track.width, 10) - parseFloat(thumb.width, 10)
    this.localRange = this.valueToRange(this.localValue)
  }
}
</script>
