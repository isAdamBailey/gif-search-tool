<template>
  <div>
    <h1>{{ msg }}</h1>
    <input v-model="searchTerm"
           type="text">
    <button class="button" @click="getGifs()">Search</button>
  </div>
</template>

<script>
  export default {
    name: 'GifSearch',
    props: {
      msg: String,
    },
    data() {
      return {
        searchTerm: '',
      };
    },
    methods: {
      getGifs() {
        let apiKey = "dc6zaTOxFJmzC";
        let searchEndPoint = "https://api.giphy.com/v1/gifs/search?";
        let limit = 5;

        let url = `${searchEndPoint}&api_key=${apiKey}&q=${
          this.searchTerm
          }&limit=${limit}`;

        fetch(url)
          .then(response => {
            return response.json();
          })
          .then(json => {
            console.log(json);
          })
          .catch(err => {
            console.log(err);
          });
      },
    },
  };
</script>

<style scoped lang="scss">
  input {
    padding: 5px;
    margin-bottom: 20px;
  }

  .button {
    background-color: rgb(0, 172, 0);
    color: white;
    padding: 5px 20px;
    border: none;
    display: block;
    margin: 0 auto;
  }

  .button:hover {
    background-color: rgb(0, 148, 0);
  }
</style>
