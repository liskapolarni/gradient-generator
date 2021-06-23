<template>
  <div id="visual-line-container"
    @mouseup="resetMouse"
    @mouseleave="resetMouse"
    @mousemove="updateMouse">
    <div id="visual-line"
      class="absolute-center border-l300"
      :style="{ background: gradient }"
      ref="line">
      <div class="color-point border-l300 pointer"
        v-for="(color, index) in gradientColors"
        :key="index"
        :class="{ selected: gradientColors[selectedColor].hex == color.hex }"
        :style="getMargin(color.position)"
        @mousedown="startMoving(color.hex, index)">
        <div class="inside-color absolute-center"
        :style="{ backgroundColor: color.hex }"></div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ['gradient-colors', 'selected-color', 'gradient-style'],
  data() {
    return {
      mouse: {
        x: 0,
        y: 0,
        selected: null,
        pressed: {
          left: false,
          right: false
        }
      }
    }
  },
  methods: {
    getMargin(position) {
      if (position < 50) {
        return {
          left: `calc(${position}% - 2px)`
        }
      } else {
        return {
          right: `calc(${100-position}% - 2px)`
        }
      }
    },
    startMoving(color, index) {
      this.$emit('select-color', color)

      Object.assign(this.mouse, {
        selected: index,
        pressed: {
          left: true,
          right: this.mouse.pressed.right
        }
      })
    },
    resetMouse() {
      Object.assign(this.mouse, {
        selected: null,
        pressed: {
          left: false,
          right: false
        }
      })
    },
    updateMouse(e) {
      const positionX = Math.floor(e.clientX-(window.innerWidth-1000)/2)-20
      if (positionX >= 0 && positionX <= this.$refs.line.clientWidth) {
        this.mouse.x = positionX
      }

      if (this.mouse.selected !== null) {
        const { hex, opacity } = this.gradientColors[this.mouse.selected]
        this.$emit('set-color-props', {
          id: this.mouse.selected,
          hex: hex,
          position: Math.round(this.mouse.x/(this.$refs.line.clientWidth/100)),
          opacity: opacity
        })
      }
    }
  },
  computed: {
    gradient() {
      return this.gradientStyle({ rotate: false, customRotation: 90 })
    }
  }
}
</script>

<style lang="scss">
@import "../sass/_variables.scss";

#visual-line-container {
  position: relative;
  width: 100%;
  height: 100px;
}

#visual-line {
  position: absolute;
  height: 20px;
  width: calc(100% - 40px);
  border-radius: 12px;
}

.color-point {
  position: absolute;
  top: -8px;
  width: 36px;
  height: 36px;
  border-radius: 18px;
  background-color: $light-100;

  &.selected {
    border: 1px solid $dark-200;
  }

  .inside-color {
    position: absolute;
    width: 18px;
    height: 18px;
    border-radius: 12px;
  }

  &:hover {
    transform: scale(1.05);
  }

  &:active {
    transform: scale(1);
  }
}
</style>