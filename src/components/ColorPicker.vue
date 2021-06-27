<template>
    <div id="color-picker"
        :class="{ dark: darkMode }">
        <slot></slot>
        
        <div id="colors" class="ml-20 mt-20">
            <div class="color-column" v-for="colorType in colors" :key="colorType">
                <div class="color"
                    v-for="color in defaultColors[colorType]"
                    :style="{ backgroundColor: color }"
                    @click="setColor(color)"
                    :key="color">
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    props: ['dark-mode'],
    data() {
        return {
            defaultColors: {
                red: ['#570100'],
                orange: ['#472a00'],
                yellow: ['#4d4400'],
                green: ['#004d10'],
                cyan: ['#00454d'],
                blue: ['#041b4a'],
                magenta: ['#4a0038'],
                purple: ['#460054'],
                black: ['#1a1a1a'],
                white: ['#858585']
            }
        }
    },
    methods: {
        setColor(color) {
            this.$emit('set-color', color)
        }
    },
    computed: {
        colors() {
            return Object.keys(this.defaultColors)
        }
    },
    mounted() {
        const Color = require('color')

        this.colors.forEach((color) => {
            const base = Color.rgb(this.defaultColors[color][0])
            for (let i in [...Array(5)]) {
                const multiplier = color == 'white' ? 0.02 : 0.1
                const modified = base.lighten((i+1)*multiplier)
                this.defaultColors[color].push(modified.hex())
            }
        })
    }
}
</script>

<style lang="scss">
@import "../sass/_variables.scss";

#color-picker {
    position: relative;
    display: inline-block;
    height: 250px;
    width: 240px;
    box-sizing: border-box;
    border-right: 1px solid $light-200;

    &.dark {
        border-right: 1px solid $dark-300;
    }
}

#colors {
    width: 200px;
    height: 120px;

    .color-column {
        display: inline-block;
        vertical-align: top;
        height: 120px;
        width: 20px;
    }

    .color {
        width: 18px;
        height: 18px;
        margin: 1px 0 0 1px;
        cursor: pointer;
        transition: all 0.1s ease;
        box-sizing: border-box;
        border-radius: 6px;

        &:hover {
            transform: scale(1.1);
            box-shadow: 2px 2px 32px rgba($dark-300, 0.2);
            border: 1px solid $dark-100;
        }

        &:active {
            transform: scale(1.05);
        }
    }
}
</style>