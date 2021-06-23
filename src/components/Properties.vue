<template>
    <div id="color-properties"
        @mouseup="resetMouse">
        <TabsSwitcher
            :selected-tab="selectedTab"
            @select-tab="selectTab" />
        <div v-if="selectedTab == 'Editor'">
            <VisualizationLine
                :gradient-colors="gradientColors"
                :selected-color="selectedColor"
                :gradient-style="gradientStyle"
                :mouse="mouse"
                @select-color="selectColor"
                @set-color-props="setColorProps" />
            <input type="text" class="ml-20 mt-20 pl-10"
                @keyup="update" v-model="input.hex" />
            <input type="text" class="ml-20 mt-20 pl-10"
                @keyup="update" v-model="input.position" />
            <input type="text" class="ml-20 mt-20 pl-10"
                @keyup="update" v-model="input.opacity" />
        </div>
        <div v-else-if="selectedTab == 'CSS'">
            <p>CSS code is going to be shown here.</p>
        </div>
    </div>
</template>

<script>
import TabsSwitcher from './TabsSwitcher.vue'
import VisualizationLine from './VisualizationLine.vue'

export default {
    components: {
        TabsSwitcher,
        VisualizationLine
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
        }
    },
    watch: {
        selectedColor: {
            handler: function() {
                this.input = {...this.gradientColors[this.selectedColor]}
            },
            deep: true
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