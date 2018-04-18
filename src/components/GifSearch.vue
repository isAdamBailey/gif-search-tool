<template>
  <div>
    <h1>{{ msg }}</h1>
    <p>Search a word or phrase, then click a result to see related GIFs</p>
    <span class="search-input__container">
      <input v-model="searchTerm"
             class="search-input"
             @keyup.enter="getWords()"
             type="text">
        <button class="search-input__button"
                @click="getWords()">Search</button>
    </span>
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
      const apiKey = 'dc6zaTOxFJmzC';
      const searchEndPoint = 'https://api.giphy.com/v1/gifs/search?';
      const limit = 6;

      const url = `${searchEndPoint}&api_key=${apiKey}&q=${this.searchTerm}&limit=${limit}`;

      fetch(url)
        .then(response => response.json())
        .then((json) => {
          this.buildGifs(json);
        })
        .catch((err) => {
          console.log(err);
        });
    },
    buildGifs(json) {
      this.gifs = json.data
        .map(gif => gif.id)
        .map(gifId => `https://media.giphy.com/media/${gifId}/giphy.gif`);
    },
    getWords() {
      this.gifs = [];
      this.words = [];
      const searchEndPoint = 'https://api.datamuse.com';
      const limit = 10;
      const proxyUrl = 'https://cors-anywhere.herokuapp.com/';
      const url = `${searchEndPoint}/words?ml=${this.searchTerm}&max=${limit}`;

      fetch(proxyUrl + url)
        .then(response => response.json())
        .then((json) => {
          this.words = json;
        })
        .catch((err) => {
          console.log(err);
        });
    },
  },
};
</script>

<style scoped lang="scss">
  .search-input {
    padding:8px 20px 8px 20px;
    margin:3px;
    font-size: 16px;
    border-radius: 20px 0 0 20px;
  }
  .search-input__button {
    transition: all .4s ease;
    background-color: purple;
    font-size:16px;
    color: white;
    padding: 8px 20px;
    width: 100px;
    border-radius: 0 20px 20px 0;
  }
  .search-input__container {
    width: 500px;
    margin: 0 auto;
  }
  .search-input__button:hover {
    background-color: lighten(purple, 15);
  }

  .gif-container {
    margin-top: 30px;
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(250px,1fr));
    grid-column-gap: 10px;
    grid-row-gap: 15px;
    align-items: center;
  }

  .gif-container__item {
    justify-self: stretch;
    padding: 10px;
    border: solid 2px lightgray;
    border-radius: 20px;
  }

  .word-container {
    margin-top: 30px;
    background: #ffffff;
    border-radius: 20px;
    display: flex;
    justify-content: space-evenly;
    align-items: center;
  }

  .word-container__item {
    padding: 10px;
  }

  .word-container__item--link {
    transition: all .4s ease;
    font-weight: bold;
    font-size: 20px;
  }

  .word-container__item--link:hover {
    cursor: pointer;
    color: purple;
    text-decoration: underline;
  }
</style>
