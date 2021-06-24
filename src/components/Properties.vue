<template>
    <div id="color-properties">
        <TabsSwitcher
            :selected-tab="selectedTab"
            @select-tab="selectTab" />
        <div v-if="selectedTab == 'Editor'">
            <VisualizationLine
                :gradient-colors="gradientColors"
                :selected-color="selectedColor"
                :gradient-style="gradientStyle"
                @select-color="selectColor"
                @set-color-props="setColorProps"
                @new-color="newColor" />
            <input type="text" class="ml-20 mt-20 pl-10"
                @keyup="update" v-model="input.hex" />
            <input type="text" class="ml-20 mt-20 pl-10"
                @keyup="update" v-model="input.position" />
            <input type="text" class="ml-20 mt-20 pl-10"
                @keyup="update" v-model="input.opacity" />
        </div>
        <div v-else-if="selectedTab == 'CSS'">
            <CodeSnippet :gradient-style="gradientStyle" />
        </div>
    </div>
</template>

<script>
import TabsSwitcher from './TabsSwitcher.vue'
import VisualizationLine from './VisualizationLine.vue'
import CodeSnippet from './CodeSnippet.vue'

export default {
    components: {
        TabsSwitcher,
        VisualizationLine,
        CodeSnippet
    },
    props: ['gradient-colors', 'selected-color', 'gradient-style'],
    data() {
        return {
            selectedTab: 'Editor',
            input: {...this.gradientColors[this.selectedColor]}
        }
    },
    methods: {
        selectTab(name) {
            this.selectedTab = name
        },
        selectColor(color) {
            this.$emit('select-color', color)
        },
        update() {
            this.$emit('update', this.input)
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
        }
    },
    watch: {
        selectedColor() {
            this.input = {...this.gradientColors[this.selectedColor]}
        },
        gradientColors: {
            handler: function() {
                this.input = {...this.gradientColors[this.selectedColor]}
            },
            deep: true
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
}

input[type=text] {
    width: 200px;
    height: 40px;
    border-radius: 8px;
    border: 1px solid $light-300;
    font-size: 18px;
}
</style>