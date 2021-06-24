<template>
  <div id="visual-line-container"
    @mouseup="resetMouse"
    @mouseleave="resetMouse"
    @mousemove="updateMouse">

    <div id="visual-line"
      class="absolute-center border-l300"
      :style="{ background: gradient }"
      ref="line">

      <div id="new-point"
        :style="{ left: `${mouse.x-10}px` }"
        v-if="!hideNewPoint"
        @click="newColor">
        +
      </div>

      <transition-group name="point">
        <div class="color-point border-l300 pointer"
          v-for="(color, index) in gradientColors"
          :key="index"
          :class="{ selected: color.id == selectedColor }"
          :style="getMargin(color.position)"
          @mousedown="startMoving(color.id)">

          <div class="inside-color absolute-center"
            :style="{ backgroundColor: color.hex }"></div>

        </div>
      </transition-group>

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
      },
      hideNewPoint: false
    }
  },
  methods: {
    getMargin(position) {
      return {
        left: `calc(${position}% - 18px)`
      }
    },
    startMoving(id) {
      this.hideNewPoint = true
      const colorObject = this.gradientColors.find((object) => object.id == id)
      this.$emit('select-color', colorObject.id)

      Object.assign(this.mouse, {
        selected: this.selectedColor,
        pressed: {
          left: true,
          right: this.mouse.pressed.right
        }
      })
    },
    resetMouse() {
      this.hideNewPoint = false

      Object.assign(this.mouse, {
        selected: null,
        pressed: {
          left: false,
          right: false
        }
      })
    },
    updateMouse(e) {
      const positionX = Math.round(e.clientX-(window.innerWidth-1000)/2)-36
      if (positionX >= 0 && positionX <= this.$refs.line.clientWidth) {
        this.mouse.x = positionX
      }
      
      const colorPosition = Math.round(this.mouse.x/(this.$refs.line.clientWidth/100))

      if (this.mouse.selected !== null) {
        const { hex, opacity, id } = this.gradientColors.find((object) => object.id == this.selectedColor)
        this.$emit('set-color-props', {
          id: id,
          hex: hex,
          position: colorPosition,
          opacity: opacity
        })
      }
    },
    newColor() {
      const position = Math.round((this.mouse.x/this.$refs.line.clientWidth)*100)
      this.$emit('new-color', position)
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
  border-bottom: 1px solid $light-200;
}

#visual-line {
  position: absolute;
  height: 20px;
  width: calc(100% - 76px);
  border-radius: 12px;

  &:hover #new-point {
    display: block;
  }
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

#new-point {
  position: absolute;
  top: 0;
  left: calc(50% - 10px);
  height: 20px;
  width: 20px;
  background-color: rgba($light-100,0.5);
  border-radius: 10px;
  line-height: 18px;
  text-align: center;
  color: $light-100;
  cursor: pointer;
  display: none;
}

.point-enter-from, .point-leave-to {
  opacity: 0.5;
  transform: scale(0.5);
}

.point-enter-to, .point-leave-from {
  opacity: 1;
  transform: scale(1);
}

.point-enter-active, .point-leave-active {
  transition: all 0.2s ease;
}
</style>