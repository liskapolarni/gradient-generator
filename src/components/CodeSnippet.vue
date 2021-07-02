<template>
    <div id="code"
        :class="{ dark: darkMode }">
        <div id="wrapper">
            <div v-for="(prefix, index) in prefixes"
                :key="index" class="snippet">
                <h3>
                    <span v-if="prefix.img">
                        <img :src="'../public/assets/icons/' + prefix.img" class="prefix-img">
                    </span>
                    {{ prefix.name }}
                </h3>
                <p>{{ prefix.description }}</p>
                <div class="line"
                    :class="{ dark: darkMode }">
                    {{ getCode(prefix) }}
                    <div class="click-to-copy"
                        :class="{ dark: darkMode }"
                        @click="copyText(getCode(prefix), prefix.name)"
                        :ref="prefix.name">
                        {{ messages.copy }}
                        <div class="btn-icon copy invert mt-3"></div>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
export default {
    props: ['gradient-style', 'dark-mode', 'messages'],
    data() {
        return {
            prefixes: [
                {
                    prefix: '',
                    img: 'web.png'
                },
                {
                    name: 'Firefox',
                    prefix: '-moz-',
                    img: 'firefox.png'
                },
                {
                    name: 'Chrome, Safari',
                    prefix: '-webkit-',
                    img: 'chrome.png'
                }
            ]
        }
    },
    methods: {
        getCode(prefix) {
            return "background: " + prefix.prefix + this.gradient + ";"
        },
        copyText(text, copyButtonRef) {
            const secretTextarea = document.createElement("textarea")
            document.body.appendChild(secretTextarea)
            secretTextarea.value = text
            secretTextarea.select()
            document.execCommand("copy")
            document.body.removeChild(secretTextarea)

            const oldValue = this.$refs[copyButtonRef].innerHTML
            this.$refs[copyButtonRef].innerHTML = this.messages.copied
            setTimeout(() => {
                if (this.$refs[copyButtonRef] !== null) {
                    this.$refs[copyButtonRef].innerHTML = oldValue
                }
            }, 2000)
        },
        updateText() {
            const changes = [
                {
                    name: this.messages.universal,
                    description: this.messages.modern
                },
                {
                    description: this.messages.legacy
                },
                {
                     description: this.messages.legacy
                }
            ]

            changes.forEach((change, index) => {
                Object.assign(this.prefixes[index], {...change})
            })
        }
    },
    computed: {
        gradient() {
            return this.gradientStyle({rotate: true})
        }
    },
    watch: {
        messages: {
            handler: function() {
                this.updateText()
            },
            deep: true
        }
    },
    mounted() {
        this.updateText()
    }
}
</script>

<style lang="scss">
@import "../sass/_variables.scss";

#code {
    position: relative;
    width: 100%;
    height: 350px;
    margin: 0;
    overflow: auto;

    #wrapper {
        position: relative;
        width: calc(100% - 40px);
        margin-left: 20px;
    }

    &.dark h3, &.dark p {
        color: $light-100;
    }
}

.snippet {
    h3 {
        font-size: 20px;
        line-height: 20px;
        margin: 20px 0 0 0;
    }

    p {
        font-size: 14px;
        line-height: 14px;
        margin: 0 0 10px 0;
        font-weight: 300;
    }

    margin-bottom: 20px;
}

.line {
    position: relative;
    width: calc(100% - 20px);
    background-color: $dark-100;
    padding: 10px 10px 40px 10px;
    border-radius: 4px;
    line-height: 20px;
    font-family: 'Arvo';
    color: $light-100;

    &.dark {
        background-color: lighten($dark-100, 10%);
    }

    .click-to-copy {
        position: absolute;
        left: 0;
        bottom: 0;
        width: 100%;
        height: 30px;
        background-color: $dark-200; 
        border-radius: 0 0 4px 4px;
        text-align: center;
        line-height: 30px;
        color: $light-100;
        font-size: 14px;
        font-family: 'Inter';
        
        &:hover {
            background-color: darken($dark-200, 4%);
        }

        &:active {
            background-color: darken($dark-200, 2%);
        }

        &.dark {
            background-color: $dark-100;

            &:hover {
                background-color: darken($dark-100, 2%);
            }

            &:active {
                background-color: darken($dark-100, 1%);
            }
        }

        cursor: pointer;
    }
}

.prefix-img {
    width: 18px;
    height: 18px;
    margin-right: 5px;
}
</style>