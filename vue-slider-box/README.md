
## 下载组件
```
npm i vue-slider-box -S
```

### 引入方式有两种，第一种在vue文件中引入
```
import VueSliderBox from 'vue-slider-box'
components: {
  VueSliderBox
},
```
### 第二种在main.js中引入并挂载到全局
```
import VueSliderBox from 'vue-slider-box'
Vue.use(VueSliderBox)
```

### 在vue文件中使用
```
<template>
  <div>
    <div class="block">
      <vue-slider-box 
        :currentData="sliderData" 
        :max="maxs" 
        :minWidth="minWidth" 
        @input="changeBar">
      </vue-slider-box>
    </div>
  </div>
</template>
<sctipt>
  export default {
    data(){
      return {
        sliderData: [0,100], // 滑块数据范围
        maxs: 666, // 总数据范围
        minWidth: 20, // 滑块最小宽度 px
      }
    },
    method: {
      changeBar (val) {
        // val为拖动滑块后改变的数据
      }
    }
  }
</script>
```