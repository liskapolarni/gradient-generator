<template>
  <main>
    <GradientGenerator
      :language="language"
      :messages="messages"
      @switch-language="switchLanguage">
      <template v-slot:title>
        <div v-if="messages">
          <h2>{{ messages.title }}</h2>
          <p>{{ messages.subtitle }}</p>
        </div>
      </template>
    </GradientGenerator>
  </main>
</template>

<script>
import GradientGenerator from './components/GradientGenerator.vue'

export default {
  name: 'App',
  components: {
    GradientGenerator
  },
  data() {
    return {
      language: 'en',
      messagesObject: null
    }
  },
  methods: {
    switchLanguage(language) {
      this.language = language
    }
  },
  computed: {
    messages() {
      return this.messagesObject !== null ? this.messagesObject[this.language] : null
    }
  },
  watch: {
    language(newValue) {
      localStorage.language = newValue
    }
  },
  mounted() {
    fetch('/json/messages.json')
      .then(response => response.json())
      .then(messages => this.messagesObject = messages)

    if (localStorage.language) {
      this.language = localStorage.language
    }
  }
}
</script>

<style lang="scss">
@import "./sass/_classes.scss";
@import "./sass/_variables.scss";
@import "./sass/_mediaqueries.scss";

body {
  margin: 0;
  font-family: 'Inter';
}

#app {
  font-family: 'Inter', sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

::selection {
  background: none;
}
</style>
