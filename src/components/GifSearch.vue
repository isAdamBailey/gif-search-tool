<template>
  <div>
    <h1>{{ msg }}</h1>
    <p>Search a word or phrase, then click a result to see related GIFs</p>
    <input v-model="searchTerm"
           @keyup.enter="getWords()"
           type="text">
    <button class="button"
            @click="getWords()">Search</button>
    <div class="word-container">
      <div v-for="word in words"
           class="word-container__item"
           :key="word.word">
        <span class="word-container__item--link"
              @click="getGifs(word.word)">
          {{ word.word }}
        </span>
      </div>
    </div>
    <div v-if="gifs"
         class="gif-container">
      <img v-for="gif in gifs"
           class="gif-container__item"
           :src="gif"
           :key="gif.id"/>
    </div>
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
        gifs: [],
        words: [],
      };
    },
    methods: {
      getGifs(word) {
        this.gifs = [];
        this.searchTerm = word;
        let apiKey = "dc6zaTOxFJmzC";
        let searchEndPoint = "https://api.giphy.com/v1/gifs/search?";
        let limit = 5;

        let url = `${searchEndPoint}&api_key=${apiKey}&q=${this.searchTerm}&limit=${limit}`;

        fetch(url)
          .then(response => {
            return response.json();
          })
          .then(json => {
            this.buildGifs(json);
          })
          .catch(err => {
            console.log(err);
          });
      },
      buildGifs(json) {
        this.gifs = json.data
          .map(gif => gif.id)
          .map(gifId => {
            return `https://media.giphy.com/media/${gifId}/giphy.gif`;
          });
      },
      getWords() {
        this.gifs = [];
        this.words = [];
        let searchEndPoint = "https://api.datamuse.com";
        let limit = 10;

        let url = `${searchEndPoint}/words?ml=${this.searchTerm}&max=${limit}`;

        fetch(url, { mode: 'no-cors' })
          .then(response => {
            return response.json();
          })
          .then(json => {
            this.words = json;
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

  .gif-container {
    margin-top: 30px;
    border-top: dotted 2px lightgray;
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  .gif-container__item {
    padding: 10px;
  }

  .word-container {
    margin-top: 30px;
    border-top: dotted 2px lightgray;
    display: flex;
    justify-content: space-evenly;
    align-items: center;
  }

  .word-container__item {
    padding: 10px;
  }

  .word-container__item--link {
    font-weight: bold;
  }

  .word-container__item--link:hover {
    cursor: pointer;
  }
</style>
