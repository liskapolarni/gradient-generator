<template>
    <div id="color-display"
        :style="{ background: gradient }">
        <Options
            :dark-mode="darkMode"
            @set-dark="setDark" />
    </div>
</template>

<script>
import Options from './Options.vue'

export default {
    props: ['gradient-colors', 'gradient-style', 'dark-mode'],
    components: {
        Options
    },
    methods: {
        setDark() {
            this.$emit('set-dark')
        }
    },
    computed: {
        gradient() {
            return this.gradientStyle({ rotate: true })
        }
    }
}
</script>

<style lang="scss">
@import "../sass/_variables.scss";

#color-display {
    position: relative;
    border-radius: 0px 12px 12px 0px;
    width: 40%;
    height: 100%;
}
</style>