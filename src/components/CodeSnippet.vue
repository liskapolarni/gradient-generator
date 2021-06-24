<template>
    <div id="code">
        <div id="wrapper">
            <div v-for="(prefix, index) in prefixes"
                :key="index" class="snippet">
                <h3>
                    <span v-if="prefix.img">
                        <img :src="'/assets/' + prefix.img" class="prefix-img">
                    </span>
                    {{ prefix.name }}
                </h3>
                <p>{{ prefix.description }}</p>
                <div class="line">
                    {{ "background: " + prefix.prefix + gradient }}
                </div>
            </div>
        </div>
    </div>
</template>

<script>

export default {
    props: ['gradient-style'],
    data() {
        return {
            prefixes: [
                {
                    name: 'Universal',
                    description: 'Works in most modern browsers',
                    prefix: '',
                    img: 'web.png'
                },
                {
                    name: 'Firefox',
                    description: 'Legacy, not needed in modern versions',
                    prefix: '-moz-',
                    img: 'firefox.png'
                },
                {
                    name: 'Chrome, Safari',
                    description: 'Legacy, not needed in modern versions',
                    prefix: '-webkit-',
                    img: 'chrome.png'
                }
            ]
        }
    },
    computed: {
        gradient() {
            return this.gradientStyle({rotate: true})
        }
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
    padding: 10px;
    line-height: 20px;
    font-size: 16px;
    font-family: 'Arvo';
    color: $light-100;
    border-radius: 4px;
}

.prefix-img {
    width: 18px;
    height: 18px;
    margin-right: 5px;
}
</style>