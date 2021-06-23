<template>
    <div id="center-box" class="absolute-center">
        <slot name="title" />
        <div id="gradient-generator-box">
            <Properties
                :gradient-colors="gradientColors"
                :selected-color="selectedColor"
                :gradient-style="getStyle"
                @update="updateGradient"
                @select-color="selectColor"
                @set-color-props="setColorProps" />
            <Display
                :gradient-colors="gradientColors"
                :gradient-style="getStyle" />
        </div>
    </div>
</template>

<script>
import Properties from './Properties.vue'
import Display from './Display.vue'

export default {
    components: {
        Properties,
        Display
    },
    data() {
        return {
            gradientColors: [
                { hex: '#F9655B', position: 0, opacity: 1 },
                { hex: '#EE821A', position: 100, opacity: 1 }
            ],
            selectedColor: 0,
            rotation: 45,
            gradientStyle: ''
        }
    },
    methods: {
        updateGradient(colorObject) {
            this.gradientColors[this.selectedColor] = {...colorObject}
        },
        selectColor(color) {
            this.selectedColor = this.gradientColors.findIndex(object => object.hex == color)
        },
        getStyle({ rotate, customRotation }) {
            const colors = this.gradientColors.map(color => `${color.hex} ${color.position}%`).join(", ")
            const rotation = rotate ? this.rotation : customRotation
            
            return `linear-gradient(${rotation}deg, ${colors})`
        },
        setColorProps(props) {
            this.gradientColors[props.id] = {
                hex: props.hex,
                position: props.position,
                opacity: props.opacity
            }
        }
    }
}
</script>

<style lang="scss">
@import "../sass/_variables.scss";

#gradient-generator-box {
    position: relative;
    display: flex;
    width: 1000px;
    height: 400px;
    margin-top: 20px;
    border: 1px solid $light-300;
    border-radius: 12px;
}

#center-box {
    margin-top: -37.5px;

    h2 {
        font-size: 32px;
        line-height: 30px;
        color: $dark-400;
        font-weight: 700;
    }

    p {
        font-size: 16px;
        line-height: 24px;
        color: $dark-200;
        font-weight: 300;
    }

    h2, p {
        text-align: center;
        margin: 0;
    }
}
</style>