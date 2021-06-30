<template>
    <div id="color-display">
        <div id="transparent-checkerboard"></div>
        <div id="gradient"
            :style="{ background: gradient }">
            <Options
                :dark-mode="darkMode"
                @set-dark="setDark" />
        </div>
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

@mixin absolutefull {
    position: absolute;
    left: 0;
    top: 0;
    width: 100%;
    height: 100%;
    border-radius: 0 12px 12px 0;
}

#transparent-checkerboard {
    @include absolutefull();

    z-index: 100;
    background-image: url('/assets/transparent.png');
}

#gradient {
    @include absolutefull();

    z-index: 200;
}
</style>