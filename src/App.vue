<template>
  <div id="app">
    <GifSearch/>
  </div>
</template>

<script>
import GifSearch from './components/GifSearch.vue';

export default {
  name: 'app',
  components: {
    GifSearch,
  },
};
</script>

<style lang="scss">
  body {
    font-family: 'Raleway', sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    background: lightslategray;
    color: #2c3e50;
  }
  h1, p {
    color: #ffffff;
  }
  h3, h2, h1 {
    font-family: 'Ubuntu', sans-serif;
  }
  #app {
    text-align: center;
    margin-top: 60px;
    grid-template-areas:
    'header'
    'input'
    'words'
    'gifs';
  }
</style>
