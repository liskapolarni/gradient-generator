<template>
    <main :class="{ dark: darkMode }">
        <div id="center-box" class="absolute-center">
            <div id="title"
                :class="{ dark: darkMode }">
                <slot name="title" />
            </div>
            <div id="gradient-generator-box"
                :class="{ dark: darkMode }">
                <Properties
                    :gradient-colors="gradientColors"
                    :selected-color="selectedColor"
                    :gradient-style="getStyle"
                    :rotation="rotation"
                    :dark-mode="darkMode"
                    @update="update"
                    @select-color="selectColor"
                    @set-color-props="setColorProps"
                    @new-color="newColor"
                    @remove-color="removeColor" />
                <Display
                    :gradient-colors="gradientColors"
                    :gradient-style="getStyle"
                    :dark-mode="darkMode"
                    @set-dark="setDarkMode" />
            </div>
        </div>
    </main>
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
            // gradient
            gradientColors: [
                { id: 0, hex: '#F9655B', position: 0, opacity: 1 },
                { id: 1, hex: '#EE821A', position: 100, opacity: 1 }
            ],
            selectedColor: 0,
            rotation: 45,
            gradientStyle: '',
            // dark mode
            darkMode: false
        }
    },
    methods: {
        update(colorObject, rotation) {
            const index = this.gradientColors.indexOf(this.gradientColors.find(object => object.id == colorObject.id))
            this.gradientColors[index] = {...colorObject}
            this.rotation = rotation
        },
        selectColor(id) {
            this.selectedColor = id
        },
        getStyle({ rotate, customRotation }) {
            const colors = this.gradientColors.map(color => `${color.hex} ${color.position}%`).join(", ")
            const rotation = rotate ? this.rotation : customRotation
            
            return `linear-gradient(${rotation}deg, ${colors})`
        },
        setColorProps(props) {
            for (let object of this.gradientColors) {
                if (object.id == props.id) {
                    Object.assign(object, {
                        hex: props.hex,
                        position: props.position,
                        opacity: props.opacity
                    })
                }
            }
        },
        newColor(position) {
            const Color = require('color')

            this.gradientColors.push({
                id: this.gradientColors.length,
                hex: '#ffffff',
                position: position,
                opacity: 1
            })

            this.sortColors()

            const index = this.gradientColors.indexOf(this.gradientColors.find(object => object.id == this.gradientColors.length-1))
            let beforeColor, afterColor

            if (index > 0) {
                const {hex, position} = this.gradientColors[index-1]
                beforeColor = { hex: hex, rgb: Color.rgb(hex).color, position: position }
            }

            if (index < this.gradientColors.length-1) {
                const {hex, position} = this.gradientColors[index+1]
                afterColor = { hex: hex, rgb: Color.rgb(hex).color, position: position }
            }

            let mixedColor
            if (beforeColor && afterColor) {
                const averageRGB = [...Array(3).keys()].map(index => (beforeColor.rgb[index]+afterColor.rgb[index])/2)
                const averageColor = Color.rgb(averageRGB).hex()

                mixedColor = averageColor
            } else if (beforeColor) {
                mixedColor = beforeColor.hex
            } else {
                mixedColor = afterColor.hex
            }

            Object.assign(this.gradientColors[index], {
                hex: mixedColor
            })
        },
        sortColors() {
            const positions = this.gradientColors.map((object) => object.position)
            let appearTwice = 0

            for (let position of positions) {
                const appearTimes = positions.filter(item => item == position).length
                if (appearTimes > 1) {
                    appearTwice += 1
                }
            }
            
            if (appearTwice == 0) {
                this.gradientColors.sort((a,b) => a.position - b.position)
            }
        },
        removeColor(colorObject) {
            let index = this.gradientColors.indexOf(colorObject)
            this.gradientColors = this.gradientColors.filter((object) => object != colorObject)

            if (index > 0) {
                index -= 1
            }

            this.selectColor(this.gradientColors[index].id)
        },
        // dark mode
        setDarkMode() {
            this.darkMode = !this.darkMode
        }
    },
    watch: {
        gradientColors: {
            handler: function() {
                this.sortColors()
            },
            deep: true
        }
    }
}
</script>

<style lang="scss">
@import "../sass/_variables.scss";

main {
    position: relative;
    width: 100vw;
    height: 100vh;
    background-color: $light-100;

    &.dark {
        background-color: $dark-100;
    }
}

#gradient-generator-box {
    position: relative;
    display: flex;
    width: 1000px;
    height: 400px;
    margin-top: 20px;
    border: 1px solid $light-300;
    border-radius: 12px;
    background-color: $light-100;

    &.dark {
        background-color: $dark-200;
        border: 1px solid $dark-300;
    }
}

#center-box {
    margin-top: -37.5px;
}

#title {
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

    &.dark h2, &.dark p {
        color: $light-100;
    }

    cursor: default;
}
</style>