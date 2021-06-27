<template>
  <main>
    <GradientGenerator>
      <template v-slot:title>
        <h2>Gradient Generator</h2>
        <p>Generate a beautiful gradient for your next project</p>
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
  }
}
</script>

<style lang="scss">
@import "./sass/_classes.scss";
@import "./sass/_variables.scss";

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
