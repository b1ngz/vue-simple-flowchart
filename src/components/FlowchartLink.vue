<template>
  <g
    @mouseover="handleMouseOver"
    @mouseleave="handleMouseLeave"
  >
    <path :d="dAttr" :style="pathStyle" />
    <a v-if="show.delete" @click="deleteLink">
      <text
        text-anchor="middle"
        :transform="arrowTransform"
        font-size="22"
      >&times;</text>
    </a>
    <path
      v-else
      d="M -1 -1 L 0 1 L 1 -1 z"
      :style="arrowStyle"
      :transform="arrowTransform"
    />
  </g>
</template>

<script>
export default {
  name: 'FlowchartLink',
  props: {
    // start point position [x, y]
    start: {
      type: Array,
      default() {
        return [0, 0]
      }
    },
    // end point position [x, y]
    end: {
      type: Array,
      default() {
        return [0, 0]
      }
    },
    id: Number
  },
  data() {
    return {
      show: {
        delete: false
      }
    }
  },
  computed: {
    pathStyle() {
      return {
        stroke: 'rgb(255, 136, 85)',
        strokeWidth: 2.73205,
        fill: 'none'
      }
    },
    arrowStyle() {
      return {
        stroke: 'rgb(255, 136, 85)',
        strokeWidth: 5.73205,
        fill: 'none'
      }
    },
    arrowTransform() {
      const [arrowX, arrowY] = this.caculateCenterPoint()
      const degree = this.caculateRotation()
      return `translate(${arrowX}, ${arrowY}) rotate(${degree})`
    },
    dAttr() {
      const cx = this.start[0]; const cy = this.start[1]; const ex = this.end[0]; const ey = this.end[1]
      const x1 = cx; const y1 = cy + 50; const x2 = ex; const y2 = ey - 50
      return `M ${cx}, ${cy} C ${x1}, ${y1}, ${x2}, ${y2}, ${ex}, ${ey}`
    }
  },
  methods: {
    handleMouseOver() {
      if (this.id) {
        this.show.delete = true
      }
    },
    handleMouseLeave() {
      this.show.delete = false
    },
    caculateCenterPoint() {
      // caculate arrow position: the center point between start and end
      const dx = (this.end[0] - this.start[0]) / 2
      const dy = (this.end[1] - this.start[1]) / 2
      return [this.start[0] + dx, this.start[1] + dy]
    },
    caculateRotation() {
      // caculate arrow rotation
      const angle = -Math.atan2(this.end[0] - this.start[0], this.end[1] - this.start[1])
      const degree = angle * 180 / Math.PI
      return degree < 0 ? degree + 360 : degree
    },
    deleteLink() {
      this.$emit('deleteLink')
    }
  }
}
</script>

<style scoped lang="scss">
g {
  cursor: pointer;
}
</style>
