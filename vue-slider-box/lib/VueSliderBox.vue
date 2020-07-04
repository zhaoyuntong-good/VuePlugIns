<template>
  <div ref="sliderBox" class="vue-slider-box-track">
    <div ref="sliderBar" class="vue-slider-box-bar" @mousedown="mouseDownBar($event)">
      <div class="left-btn" @mousedown.stop="mouseDownLeft($event)"></div>
      <div class="right-btn" @mousedown.stop="mouseDownRight($event)"></div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'VueSliderBox',
  props: {
    currentData: {
      type: Array,
      default: [0,10],
      required: true
    },
    max: {
      type: Number,
      default: 0
    },
    minWidth: {
      type: Number,
      default: 20
    }
  },
  data(){
  	return {
      maxData: null,
      step: null,
      dragInBar: null,
      dragLeftBtn: null,
      parentWidth: null,
      dragRightBtn: null,
  	}
  },
  created(){
    if (this.max === 0) {
      this.maxData = this.currentData[1];
    } else {
      this.max >= this.currentData[1] ? this.maxData = this.max : this.maxData = this.currentData[1];
    }
  },
  mounted(){
    this.parentWidth = this.$refs.sliderBox.clientWidth;
    this.step = this.maxData / this.parentWidth;
    this.$refs.sliderBar.style.left =  this.currentData[0] / this.step + 'px';
    this.$refs.sliderBar.style.width = (this.currentData[1] - this.currentData[0]) / this.step + 'px';
    document.onmouseup = () => {
      this.removeListrner();
    }
  },
  methods: {
    mouseDownBar(event){
      const mouseCurrentX = event.clientX;
      const domCurrentLeft = this.$refs.sliderBar.offsetLeft;
      const domCurrentWidth = this.$refs.sliderBar.offsetWidth;
      const maxLeft = this.parentWidth - domCurrentWidth;
      let preX = event.clientX;
      this.removeListrner();
      this.dragInBar = event => {
        let changeLeft = event.clientX - mouseCurrentX;
        this.$refs.sliderBar.style.left = domCurrentLeft + changeLeft + 'px';
        if (changeLeft < 0 && this.$refs.sliderBar.offsetLeft < 0) {
          this.$refs.sliderBar.style.left = 0;
        }
        if (changeLeft > 0 && this.$refs.sliderBar.offsetLeft > maxLeft) {
          this.$refs.sliderBar.style.left = maxLeft + 'px';
        }
        let leftNum = this.$refs.sliderBar.offsetLeft * this.step;
        let rightNum = (this.$refs.sliderBar.offsetWidth + this.$refs.sliderBar.offsetLeft) * this.step;
        let val = null;
        if (event.clientX - preX > 0) {
          val = [Math.floor(leftNum), Math.floor(rightNum)]
        } else {
          val = [Math.ceil(leftNum), Math.ceil(rightNum)]
        }
        this.$emit('input', val)
        preX = event.clientX;
      }
      document.addEventListener('mousemove',this.dragInBar);
    },
    mouseDownLeft (event) {
      const mouseCurrentX = event.clientX;
      const domCurrentLeft = this.$refs.sliderBar.offsetLeft;
      const domCurrentWidth = this.$refs.sliderBar.offsetWidth;
      this.removeListrner();
      this.dragLeftBtn = event => {
        let changeLeft = event.clientX - mouseCurrentX;
        this.$refs.sliderBar.style.width = domCurrentWidth - changeLeft + 'px';
        let movingDomWidth = this.$refs.sliderBar.offsetWidth;
        this.$refs.sliderBar.style.left = domCurrentLeft + changeLeft + 'px';
        let movingDomLeft = this.$refs.sliderBar.offsetLeft;
        if (movingDomLeft < 0) {
          this.$refs.sliderBar.style.left = 0;
          this.$refs.sliderBar.style.width = domCurrentWidth + domCurrentLeft + 'px';
        }
        if (movingDomWidth <= this.minWidth) {
          this.$refs.sliderBar.style.width = this.minWidth + 'px';
          this.$refs.sliderBar.style.left = domCurrentLeft + domCurrentWidth - this.minWidth + 'px'
        }
        let leftNum = this.$refs.sliderBar.offsetLeft * this.step;
        let val = [Math.floor(leftNum), this.currentData[1]]
        this.$emit('input', val)
      }
      document.addEventListener('mousemove',this.dragLeftBtn); 
    },
    mouseDownRight (event) {
      const mouseCurrentX = event.clientX;
      const domCurrentLeft = this.$refs.sliderBar.offsetLeft;
      const domCurrentWidth = this.$refs.sliderBar.offsetWidth;
      this.removeListrner();
      this.dragRightBtn = event => {
        let changeLeft = event.clientX - mouseCurrentX;
        this.$refs.sliderBar.style.width = domCurrentWidth + changeLeft + 'px';
        let movingDomWidth = this.$refs.sliderBar.offsetWidth;
        if (domCurrentLeft + movingDomWidth > this.parentWidth) {
          this.$refs.sliderBar.style.width = this.parentWidth - domCurrentLeft + 'px';
        }
        if (movingDomWidth <= this.minWidth) {
          this.$refs.sliderBar.style.width = this.minWidth + 'px';
        }
        let rightNum = (this.$refs.sliderBar.offsetWidth + this.$refs.sliderBar.offsetLeft) * this.step;
        let val = [this.currentData[0], Math.ceil(rightNum)]
        this.$emit('input', val)
      }
      document.addEventListener('mousemove',this.dragRightBtn);
    },
    removeListrner(){
      document.removeEventListener('mousemove',this.dragInBar);
      this.dragInBar = null;
      document.removeEventListener('mousemove',this.dragLeftBtn);
      this.dragLeftBtn = null;
      document.removeEventListener('mousemove',this.dragRightBtn);
      this.dragRightBtn = null;
    }
  },
  beforeDestroy(){
    this.removeListrner();
  }
}
</script>

<style scoped>
  div {
    box-sizing: border-box;
  }
  .vue-slider-box-track {
    position: relative;
    width: 100%;
    height: 100%;
    border: 1px solid rgba(231,235,240,1);
  }
  .vue-slider-box-bar {
    position: absolute;
    height: calc(100% + 2px);
    top: -1px;
    z-index: 1;
    background-color: rgba(216,225,235,.5);
    border-left: 1px solid rgba(216,225,235,1);
    border-right: 1px solid rgba(216,225,235,1);
    cursor: e-resize;
  }
  .vue-slider-box-bar .left-btn {
    float: left;
    position: relative;
    width: 8px;
    height: 14px;
    margin-top: 8px;
    margin-left: -4px;
    background-color: rgba(216,225,235,1);
    cursor: e-resize;
  }
  .vue-slider-box-bar .right-btn {
    float: right;
    position: relative;
    width: 8px;
    height: 14px;
    margin-top: 8px;
    margin-right: -4px;
    background-color: rgba(216,225,235,1);
    cursor: e-resize;
  }
</style>