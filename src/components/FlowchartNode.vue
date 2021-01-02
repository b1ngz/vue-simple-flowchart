<template>
  <div
    class="flowchart-node"
    :style="nodeStyle"
    :class="{selected: options.selected === id}"
    @mousedown="handleMousedown"
    @mouseover="handleMouseOver"
    @mouseleave="handleMouseLeave"
    @click="onClick"
  >
    <div
      class="node-port node-input"
      @mousedown="inputMouseDown"
      @mouseup="inputMouseUp"
    />
    <div class="node-main">
      <div class="node-type" v-text="type" />
      <div class="node-label" v-text="label" />
    </div>
    <div
      class="node-port node-output"
      @mousedown="outputMouseDown"
    />
    <div v-show="show.delete" class="node-delete">&times;</div>
  </div>
</template>

<script>
export default {
  name: 'FlowchartNode',
  props: {
    id: {
      type: Number,
      default: 1000,
      validator(val) {
        return typeof val === 'number'
      }
    },
    x: {
      type: Number,
      default: 0,
      validator(val) {
        return typeof val === 'number'
      }
    },
    y: {
      type: Number,
      default: 0,
      validator(val) {
        return typeof val === 'number'
      }
    },
    type: {
      type: String,
      default: 'Default'
    },
    label: {
      type: String,
      default: 'input name'
    },
    options: {
      type: Object,
      default() {
        return {
          centerX: 1024,
          scale: 1,
          centerY: 140
        }
      }
    }
  },
  data() {
    return {
      show: {
        delete: false
      },
      delay: 700,
      clicks: 0
    }
  },
  computed: {
    nodeStyle() {
      return {
        top: this.options.centerY + this.y * this.options.scale + 'px', // remove: this.options.offsetTop +
        left: this.options.centerX + this.x * this.options.scale + 'px', // remove: this.options.offsetLeft +
        transform: `scale(${this.options.scale})`
      }
    }
  },
  mounted() {
  },
  methods: {
    handleMousedown(e) {
      const target = e.target || e.srcElement
      // console.log(target);
      if (target.className.indexOf('node-input') < 0 && target.className.indexOf('node-output') < 0) {
        this.$emit('nodeSelected', e)
      }
      e.preventDefault()
    },
    handleMouseOver() {
      this.show.delete = true
    },
    handleMouseLeave() {
      this.show.delete = false
    },
    outputMouseDown(e) {
      const rect = e.target.getBoundingClientRect()
      this.$emit('linkingStart', this.id, rect)
      e.preventDefault()
    },
    inputMouseDown(e) {
      e.preventDefault()
    },
    inputMouseUp(e) {
      const rect = e.target.getBoundingClientRect()
      this.$emit('linkingStop', this.id, rect)
      e.preventDefault()
    },
    onClick: function(event) {
      this.clicks++
      if (this.clicks === 1) {
        const self = this
        this.timer = setTimeout(function() {
          self.clicks = 0
        }, this.delay)
      } else {
        clearTimeout(this.timer)
        this.$emit('nodeEdit')
        this.clicks = 0
      }
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">
$themeColor: rgb(255, 136, 85);
$portSize: 12;

.flowchart-node {
  margin: 0;
  position: absolute;
  box-sizing: border-box;
  border: none;
  background: white;
  z-index: 1;
  opacity: .9;
  cursor: move;
  transform-origin: top left;
  .node-main {
    text-align: center;
    .node-type {
      background: $themeColor;
      color: white;
      font-size: 13px;
      padding: 6px;
    }
    .node-label {
      padding: 6px;
      font-size: 13px;
    }
  }
  .node-port {
    position: absolute;
    width: #{$portSize}px;
    height: #{$portSize}px;
    left: 50%;
    transform: translate(-50%);
    border: 1px solid #ccc;
    border-radius: 100px;
    background: white;
    &:hover {
      background: $themeColor;
      border: 1px solid $themeColor;
    }
  }
  .node-input {
    top: #{-2+$portSize/-2}px;
  }
  .node-output {
    bottom: #{-2+$portSize/-2}px;
  }
  .node-delete {
    position: absolute;
    right: -6px;
    top: -6px;
    font-size: 12px;
    width: 12px;
    height: 12px;
    color: $themeColor;
    cursor: pointer;
    background: white;
    border: 1px solid $themeColor;
    border-radius: 100px;
    text-align: center;
    &:hover{
      background: $themeColor;
      color: white;
    }
  }
}
.selected {
  box-shadow: 0 0 0 2px $themeColor;
}
</style>
