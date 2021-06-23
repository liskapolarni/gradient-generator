<template>
    <div id="color-display"
        :style="{ background: gradient }">

    </div>
</template>

<script>
export default {
    props: ['gradient-colors', 'gradient-style'],
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
    border-radius: 0px 12px 12px 0px;
    width: 40%;
    height: 100%;
}
</style>