<template>
    <div id="tabs">
        <div v-for="(name, index) in tabs"
            :key="index" class="tab pointer"
            :class="{
                topleftradius: index == 0,
                selected: selectedTab == name 
            }"
            @click="selectTab(name)">
            {{ name }}
        </div>
    </div>
</template>

<script>
export default {
    props: ['selected-tab'],
    data() {
        return {
            tabs: ['Editor', 'CSS']
        }
    },
    methods: {
        selectTab(name) {
            this.$emit('select-tab', name)
        }
    }
}
</script>

<style lang="scss">
@import "../sass/_variables.scss";

#tabs {
    height: 50px;
    width: 100%;
    box-sizing: border-box;
    border-bottom: 1px solid $light-200;
    border-top-left-radius: 12px;
}

.tab {
    display: inline-block;
    vertical-align: top;
    padding: 0 20px 0 20px;
    text-align: center;
    color: $gray-100;
    font-size: 16px;
    line-height: 50px;
    height: 50px;

    &:hover {
        color: $dark-100;
    }

    &:active {
        background-color: darken($light-100, 3%);
        height: 49px;
    }

    &.selected {
        color: $dark-100;
        border-bottom: 2px solid $primary;
        height: 48px;
    }

    &.topleftradius {
        border-top-left-radius: 12px;
    }
}
</style>