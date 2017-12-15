<template>
  <article>
    <h1><code>&lt;veui-slider&gt;</code></h1>
    <section>
      <h2>默认样式</h2>
      <veui-slider :value.sync="value1" :liveDragging="true" key="ttt"></veui-slider>
    </section>
    <section>
      <h2>设置参数样式</h2>
      <veui-slider :value.sync="value2" :liveDragging="false" key="yyy"></veui-slider>
    </section>
    <section>
      <h2>自定义tips内容</h2>
      <veui-slider :value="77" :liveDragging="true" class="slider3"></veui-slider>
    </section>
    <section>
      <h2>禁用</h2>
      <veui-slider :value="value4"></veui-slider>
    </section>
    <section>
      <h2>垂直</h2>
      <veui-slider :value="77"></veui-slider>
    </section>
    <section>
      <h2>颜色调节器</h2>
      <veui-slider :value="77"></veui-slider>
    </section>
  </article>
</template>

<script>
import { Button, Slider } from 'veui'

export default {
  name: 'slider-demo',
  components: {
    'veui-button': Button,
    'veui-slider': Slider
  },
  data () {
    return {
      value1: 40,
      value2: 33,
      value4: 20
    }
  },
  watch: {
    value1 (val) {
      // console.log('slider-demo1: ' + val)
    },
    value2 (val) {
      console.log('slider-demo2: ' + val)
    },
    value4 (val) {
      console.log('slider-demo4: ' + val)
    }
  }
}
</script>

<style lang="less" scoped>
section {
  margin-bottom: 30px;
}
</style>
