<template>
  <div id="app">
    <GifSearch msg="Search Giphy"/>
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
    background: lightslategray;
  }
  h1, p {
    color: #ffffff;
  }
  h3, h2, h1 {
    font-family: 'Ubuntu', sans-serif;
  }
  #app {
    font-family: 'Raleway', sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    margin-top: 60px;
  }
</style>
