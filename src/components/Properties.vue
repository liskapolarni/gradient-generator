<template>
    <div id="color-properties"
        v-if="messages"
        :class="{ dark: darkMode }">
        <TabsSwitcher
            :selected-tab="selectedTab"
            :dark-mode="darkMode"
            :messages="messages"
            @select-tab="selectTab"
            @shuffle="shuffle" />
        <div v-if="selectedTab == 'Editor'">
            <VisualizationLine
                :gradient-colors="gradientColors"
                :selected-color="selectedColor"
                :gradient-style="gradientStyle"
                :dark-mode="darkMode"
                @select-color="selectColor"
                @set-color-props="setColorProps"
                @new-color="newColor" />
            <ColorPicker
                :dark-mode="darkMode"
                @set-color="setColor">
                <div class="input-box w-240 mt-20">
                    <label for="color">{{ messages.color }} (hex)</label>
                    <input type="text" class="ml-20 mt-20 pl-10" name="color"
                        @keyup="update" v-model="input.hex" />
                </div>
            </ColorPicker>

            <div id="inputs">
                <div class="input-box halfwidth mt-20">
                    <label for="position">{{ messages.position }} (0-100)</label>
                    <input type="text" name="position"
                        @keyup="update" v-model="input.position" />
                </div>

                <div class="input-box halfwidth mt-20">
                    <label for="opacity">{{ messages.opacity }} (0-1)</label>
                    <input type="text" name="opacity"
                        @keyup="update" v-model="input.opacity" />
                </div>

                <div class="input-box fullwidth mt-20">
                    <label for="rotation">{{ messages.rotation }} (°)</label>
                    <input type="text" name="rotation"
                        @keyup="update" v-model="inputRotation" />
                </div>

                <div class="btn relative-center mt-20"
                    :class="{
                        red: gradientColors.length > 2,
                        darkmode: darkMode
                    }"
                    @click="removeColor">
                    <div class="btn-icon delete"
                        :class="{
                            opacityhalf: gradientColors.length <= 2 && !darkMode,
                            invert: gradientColors.length > 2
                        }">
                    </div>
                    {{ messages.removePoint }}
                </div>
            </div>
        </div>
        <div v-else-if="selectedTab == 'CSS'">
            <CodeSnippet
                :gradient-style="gradientStyle"
                :dark-mode="darkMode"
                :messages="messages" />
        </div>
    </div>
</template>

<script>
import TabsSwitcher from './TabsSwitcher.vue'
import VisualizationLine from './VisualizationLine.vue'
import ColorPicker from './ColorPicker.vue'
import CodeSnippet from './CodeSnippet.vue'

export default {
    components: {
        TabsSwitcher,
        VisualizationLine,
        ColorPicker,
        CodeSnippet
    },
    props: ['gradient-colors', 'selected-color', 'gradient-style', 'rotation', 'dark-mode', 'messages'],
    data() {
        return {
            selectedTab: 'Editor',
            input: {...this.gradientColors.find(object => object.id == this.selectedColor)},
            inputRotation: this.rotation,
            lastValidColor: this.gradientColors[0].hex
        }
    },
    methods: {
        selectTab(name) {
            this.selectedTab = name

            // in case some value is left empty, reset inputs
            const resetValues = {
                position: 0,
                opacity: 1
            }

            Object.keys(resetValues).forEach((key) => {
                if (this.input[key].toString().length == 0) {
                    this.input[key] = resetValues[key]
                }
            })

            if (this.inputRotation.toString().length == 0) {
                this.inputRotation = 45
            }
            
            // revert to last valid color if the currently
            // entered color doesn't meet required criteria
            if (![4,7].includes(this.input.hex.length)) {
                this.input.hex = this.lastValidColor
            }
            this.$emit('update', this.input, this.inputRotation)
        },
        selectColor(id) {
            this.$emit('select-color', id)
        },
        update() {
            // color validation
            if (this.input.hex.length < 1 || this.input.hex.length > 7) {
                this.input.hex = "#FFFFFF"
            }

            // position and opacity validation
            const limits = {
                position: 100.0,
                opacity: 1.0
            }

            Object.keys(limits).forEach((property) => {
                this.input[property] = this.input[property].toString().match(/^\d*\.?\d*$/, "")
                if (parseFloat(this.input[property]) > limits[property]) {
                    this.input[property] = limits[property]
                }
            })

            // rotation validation
            this.inputRotation = this.inputRotation.toString().replace(/[^\d]/g, "")

            if ([4,7].includes(this.input.hex.length)) {
                this.lastValidColor = this.input.hex
                this.$emit('update', this.input, this.inputRotation)
            }
        },
        setColorProps(props) {
            this.$emit('set-color-props', {
                id: props.id,
                hex: props.hex,
                position: props.position,
                opacity: props.opacity
            })
        },
        newColor(position) {
            this.$emit('new-color', position)
        },
        setColor(color) {
            this.lastValidColor = color
            this.setColorProps({
                id: this.selectedColor,
                hex: color,
                position: this.colorObject.position,
                opacity: this.colorObject.opacity
            })
        },
        removeColor() {
            if (this.gradientColors.length > 2) {
                this.$emit('remove-color', this.colorObject)
            }
        },
        shuffle() {
            this.$emit('shuffle')
        }
    },
    watch: {
        selectedColor() {
            this.input = {...this.colorObject}
        },
        rotation() {
            this.inputRotation = this.rotation
        },
        gradientColors: {
            handler: function() {
                this.input = {...this.colorObject}
            },
            deep: true
        }
    },
    computed: {
        colorObject() {
            return this.gradientColors.find(object => object.id == this.selectedColor)
        }
    }
}
</script>

<style lang="scss">
@import "../sass/_variables.scss";

#color-properties {
    position: relative;
    border-radius: 12px 0 0 12px;
    width: 60%;
    height: 100%;

    &.dark {
        label {
            color: $light-100;
        }

        input[type=text] {
            border: none;
            background-color: $dark-100;
            color: $light-100;
        }
    }
}

input[type=text] {
    width: 200px;
    height: 40px;
    border-radius: 8px;
    border: 1px solid $light-300;
    font-size: 18px;
    box-sizing: border-box;
}

#inputs {
    display: inline-block;
    vertical-align: top;
    height: 250px;
    width: 360px;

    .fullwidth {
        width: 360px;
    }

    .halfwidth {
        width: 180px;
    }
}

.input-box {
    display: inline-block;

    input {
        width: calc(100% - 40px);
        margin: 25px 0 0 20px;
        padding-left: 10px;
    }

    label {
        position: absolute;
        padding-left: 20px;
        color: $dark-100;
        font-size: 12px;
        text-transform: uppercase;
    }
}

.w-240 {
    width: 240px;
}
</style>