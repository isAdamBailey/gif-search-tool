<template>
  <div>
    <div class="header__container">
      <h1>Gif Search Sprint Planning Tool</h1>
    </div>
    <div class="search-input__container">
      <input v-model="searchTerm"
             class="search-input"
             @keyup.enter="explore()"
             type="text">
      <span class="search-input__button--group">
        <button class="search-input__button"
                @click="justOne()">Just Give Me One</button>
        <button class="search-input__button"
                @click="explore()">Explore</button>
      </span>
    </div>
    <div :class="wordContainerClass"
         v-if="words.length">
      <h3 v-if="!singleItem" class="word-container__item--header">
        Click words below to generate more possibilities
      </h3>
      <div v-if="!singleItem" class="word-container__item">
        <div v-for="word in words"
             :key="word.word"
             class="word-container__item--link"
             @click="onWordClick(word.word)">
            {{ capital(word.word) }}
        </div>
      </div>
      <h3 class="word-container__item--header">
        Sprint names generated from <u>{{ searchTerm }}</u>
      </h3>
      <div class="word-container__item">
        <div v-for="(word, index) in words"
             :class="wordContainerItemTextClass"
             :key="index">
          {{ adjectives[index] ? capital(adjectives[index].word) : '' }}
          {{ capital(word.word) }}
          {{ nounsByAdjectives[index] ? capital(nounsByAdjectives[index].word) : '' }}
        </div>
      </div>
    </div>
    <div v-if="gifs"
         :class="gifContainerClass">
      <div v-for="gif in gifs"
           class="gif-container__item"
           :key="gif.id">
        <img :src="gif.src"/>
        <div class="gif-container__item--url">{{ gif.src }}</div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'GifSearch',
  data() {
    return {
      searchTerm: '',
      gifs: [],
      words: [],
      adjectives: [],
      nounsByAdjectives: [],
      singleItem: false,
    };
  },
  computed: {
    gifContainerClass() {
      return this.singleItem ? 'gif-container-single' : 'gif-container';
    },
    wordContainerClass() {
      return this.singleItem ? 'word-container-single' : 'word-container';
    },
    wordContainerItemTextClass() {
      return this.singleItem ? 'word-container__item--text-single' : 'word-container__item--text';
    },
  },
  methods: {
    onWordClick(word) {
      this.searchTerm = word;
      return this.explore();
    },
    explore() {
      this.singleItem = false;
      this.getMeaningLikes();
      this.getAdjectives();
      this.getNounsByAdjectives();
    },
    justOne() {
      this.singleItem = true;
      this.getMeaningLikes(1);
      this.getAdjectives(1);
      this.getNounsByAdjectives(1);
    },
    getGifs(word, max) {
      this.gifs = [];
      this.searchTerm = word;
      const apiKey = 'dc6zaTOxFJmzC';
      const searchEndPoint = 'https://api.giphy.com/v1/gifs/search?';

      const url = `${searchEndPoint}&api_key=${apiKey}&q=${this.searchTerm}&limit=${max}`;

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
      this.gifs = json.data.map(gif => ({
        src: `https://media.giphy.com/media/${gif.id}/giphy.gif`,
      }));
    },
    fetchDatamuse(query, bucket, string, max) {
      this.gifs = [];
      const searchEndPoint = 'https://api.datamuse.com';
      const proxyUrl = 'https://cors-anywhere.herokuapp.com/';
      const url = `${searchEndPoint}${query}${string}&max=${max}`;

      this.getGifs(this.searchTerm, max);

      fetch(proxyUrl + url)
        .then(response => response.json())
        .then((json) => {
          this[`${bucket}`] = json;
        })
        .catch((err) => {
          console.log(err);
        });
    },
    async getMeaningLikes(max = 6) {
      await this.fetchDatamuse('/words?ml=', 'words', this.searchTerm, max);
    },
    async getAdjectives(max = 6) {
      await this.fetchDatamuse('/words?rel_jjb=', 'adjectives', this.searchTerm.split(' ', 1), max);
    },
    async getNounsByAdjectives(max = 6) {
      await this.fetchDatamuse('/words?rel_jja=', 'nounsByAdjectives', this.searchTerm.split(' ', 1), max);
    },
    capital(word) {
      return `${word.charAt(0).toUpperCase()}${word.slice(1)}`;
    },
  },
};
</script>

<style scoped lang="scss">
  .header__container {
    grid-area: header;
    padding: 20px;
    border: 3px dashed #ffffff;
    border-radius: 5px;
    background: #2c3e50;
  }

  .search-input {
    padding: 10px 20px;
    font-size: 20px;
    border-radius: 5px;
    margin: 5px 0;
  }
  .search-input__button--group {
    display: flex;
    justify-content: space-evenly;
    margin: 5px 0;
  }
  .search-input__button {
    transition: all .4s ease;
    background-color: purple;
    font-size:20px;
    color: white;
    padding: 12px 20px;
    margin: 0 10px;
    border: none;
    border-radius: 5px;
  }
  .search-input__container {
    grid-area: input;
    display: flex;
    justify-content: space-evenly;
    align-items:flex-end;
    flex-wrap: wrap;
    padding: 30px 0 10px 0;
    max-width: 700px;
    margin: 0 auto;
  }
  .search-input__button:hover {
    background-color: darken(purple, 8);
  }

  .gif-container {
    grid-area: gifs;
    margin-top: 30px;
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(300px,1fr));
    grid-column-gap: 15px;
    grid-row-gap: 15px;
    align-items: center;
  }
  .gif-container-single {
    margin-top: 30px;
    display: grid;
  }
  .gif-container__item {
    display: grid;
    grid-template-rows: 3fr 1fr;
    grid-row-gap: 5px;
    justify-self: center;
    align-self: start;
    padding: 10px;
    background-color: #ffffff;
    border: solid 2px lightgray;
    border-radius: 5px;
  }
  .gif-container__item img {
    max-width: 100%;
    justify-self: center;
  }
  .gif-container__item--url {
    font-weight: bold;
    align-self: center;
    justify-self: center;
    width: 300px;
    word-wrap: break-word;
  }

  .word-container {
    grid-area: words;
    margin-top: 30px;
    padding-bottom: 30px;
    background: #ffffff;
    border: 3px dashed #2c3e50;
    border-radius: 5px;
    display: grid;
    grid-template-rows: 1fr;
  }
  .word-container-single {
    margin: 30px auto;
    padding-bottom: 30px;
    max-width: 700px;
    background: #ffffff;
    border: 3px dashed #2c3e50;
    border-radius: 5px;
  }
  .word-container__item {
    display: flex;
    justify-content: space-evenly;
    flex-wrap: wrap;
  }
  .word-container__item--link {
    border-radius: 10%;
    padding: 10px;
    transition: all .4s ease;
    font-weight: bold;
    font-size: 20px;
  }
  .word-container__item--link:hover {
    cursor: pointer;
    color: white;
    background-color: darken(purple, 8);
  }
  .word-container__item--text {
    font-size: 20px;
    margin: 0 20px;
  }
  .word-container__item--text-single {
    font-size: 40px;
    font-weight: bold;
    margin: 0 20px;
  }
  .word-container__item--header {
    font-size: 22px;
    justify-self: center;
    padding-bottom: 10px;
    border-bottom: 1px solid lightgray;
  }
</style>
