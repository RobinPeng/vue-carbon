<template>
<div class="popover" v-show="show" :style="{'top': pos.top + 'px', 'left': pos.left + 'px'}"
  :class="{'popover-top': pos.position === 'top', 'popover-bottom': pos.position === 'bottom'}"
  transition="popover">
  <div class="popover-content">
    <slot></slot>
  </div>
</div>
</template>
<script>
import Popup from '../popup'
import * as domUtil from '../utils/domUtil'
export default {
  mixins: [Popup],
  props: {
    trigger: {
      type: [Array, window.Element],
      default () {
        return []
      }
    }
  },
  data () {
    return {
      pos: {
        left: 0,
        top: 0,
        position: 'top'
      }
    }
  },
  attached () {
    this.hanlderOpen = (e) => this.open(e)
    if (this.trigger instanceof window.Element) {
      this.trigger.addEventListener('click', this.hanlderOpen, false)
    } else {
      this.trigger.forEach((el) => {
        el.addEventListener('click', this.hanlderOpen, false)
      })
    }
  },
  detached () {
    if (this.trigger instanceof window.Element) {
      this.trigger.removeEventListener('click', this.hanlderOpen, false)
    } else {
      this.trigger.forEach((el) => {
        el.removeEventListener('click', this.hanlderOpen, false)
      })
    }
  },
  methods: {
    open (e) {
      const el = e.target
      this.show = true
      this.pos = this.computePos(this.getEleInfo(el))
    },
    getEleInfo (el) {
      let offset = domUtil.getOffset(el)
      return {
        left: offset.left,
        top: offset.top,
        width: el.offsetWidth,
        height: el.offsetHeight
      }
    },
    computePos (elInfo) {
      const space = 16
      let position = 'bottom'
      let left = elInfo.left
      let top = elInfo.top
      this.$el.style.display = ''
      const ctWw = this.$el.offsetWidth
      const ctWh = this.$el.offsetHeight
      this.$el.style.display = 'none'
      const ww = window.innerWidth
      const wh = window.innerHeight
      if (wh - elInfo.top - space < ctWh) {
        position = 'top'
        top = elInfo.top + elInfo.height - ctWh
      }

      if (ww - elInfo.left - space < ctWw) {
        left = elInfo.left + elInfo.width - ctWw
      }
      return {
        position: position,
        left: left,
        top: top
      }
    },
    overlayClick () {
      this.show = false
    }
  }
}
</script>

<style lang="less">
@import "../utils/_mixins.less";
.popover{
  position: fixed;
  background: #FFF;
  max-width: 300px;
  min-width: 200px;
  border-radius: 3px;
  .depth(1);
  &.popover-bottom {
    transform-origin: center top;
  }
  &.popover-top{
    transform-origin: center bottom;
  }
}

.popover-content{
  overflow-y: auto;
  overflow-x: hidden;
  -webkit-overflow-scrolling: touch;
  max-height: 70%;
}

.popover-transition{
  transition-duration: 300ms;
  transition-property: all;
  &.popover-enter {
    transform: scale(0);
    opacity: 0;
  }
  &.popover-leave {
    opacity: 0;
  }
}

</style>
