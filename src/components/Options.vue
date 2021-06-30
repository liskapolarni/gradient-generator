<template>
    <div id="btn-showoptions" class="invert"
        :class="{ close: showOptions, showmore: !showOptions }"
        @click="setShowOptions">
    </div>

    <transition name="fade">
        <div id="options-menu" v-if="showOptions">
            <ul>
                <li @click="setDarkMode">
                    Color mode: {{ colorMode }}
                </li>
                <li>
                    Language: <img :src="language" alt="flag">
                </li>
            </ul>
        </div>
    </transition>
</template>

<script>
export default {
    props: ['dark-mode'],
    emits: ['set-dark'],
    data() {
        return {
            showOptions: false,
            tempLanguage: 'en'
        }
    },
    methods: {
        setShowOptions() {
            this.showOptions = !this.showOptions
        },
        setDarkMode() {
            this.$emit('set-dark')
        }
    },
    computed: {
        colorMode() {
            switch(this.darkMode) {
                case true:
                    return "🌙"
                case false:
                    return "☀️"
            }
        },
        language() {
            const countryCode = {
                en: 'gb',
                cz: 'cz'
            }
            return `https://flagcdn.com/24x18/${countryCode[this.tempLanguage]}.png`
        }
    }
}
</script>

<style lang="scss">
@import "../sass/_variables.scss";

#btn-showoptions {
    position: absolute;
    z-index: 100;
    top: 20px;
    right: 20px;
    width: 40px;
    height: 40px;
    background-size: 30px 30px;
    background-repeat: no-repeat;
    background-position: 5px;
    background-color: rgba($dark-100, 0.25);
    border-radius: 20px;
    box-shadow: 2px 2px 32px rgba($light-100, 0.1);

    &:hover {
        background-color: rgba($dark-100, 0.4);
    }

    &:active {
        background-color: rgba($dark-100, 0.325);
    }

    transition: all 0.1s ease;
    cursor: pointer;
}

#options-menu {
    position: absolute;
    z-index: 50;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba($dark-100, 0.75);
    border-radius: 0 12px 12px 0;

    ul {
        list-style: none;
        position: absolute;
        left: 50%;
        top: 50%;
        transform: translateX(-50%) translateY(-50%);
        padding: 0;
        margin: 0;

        li {
            color: $light-100;
            font-size: 24px;
            line-height: 32px;
            text-align: center;
            cursor: pointer;
            padding: 5px 10px 5px 10px;
            border-radius: 8px;

            &:hover {
                background-color: rgba($light-100, 0.25);
            }

            &:active {
                transform: scale(0.975);
            }

            transition: all 0.1s ease;

            img {
                padding-left: 10px;
            }
        }
    }
}

.fade-enter-from, .fade-leave-to {
    opacity: 0;
}

.fade-enter-to, .fade-leave-from {
    opacity: 1;
}

.fade-enter-active, .fade-leave-active {
    transition: all 0.1s ease;
}
</style>