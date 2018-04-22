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
    <div class="word-container"
         v-if="words.length">
      <h3 class="word-container__item--header">
        {{ searchTerm ? `Words related to ${searchTerm}:` : '' }}
      </h3>
      <div class="word-container__item">
        <div v-for="word in words"
             :key="word.word"
             class="word-container__item--link"
             @click="getGifs(word.word)">
            {{ word.word }}
        </div>
      </div>
      <h3 class="word-container__item--header">
        {{ searchTerm ? `Sprint names summoned from ${searchTerm}:` : '' }}
      </h3>
      <div class="word-container__item">
        <div v-for="(word, index) in words"
             class="word-container__item--text"
             :key="index">
          {{ word.word }}
          {{ adjectives[index] ? adjectives[index].word : '' }}
          {{ rhymesWiths[index] ? rhymesWiths[index].word : '' }}
        </div>
      </div>
    </div>
    <div v-if="gifs"
         class="gif-container">
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
  props: {
    msg: String,
  },
  data() {
    return {
      searchTerm: '',
      gifs: [],
      words: [],
      rhymesWiths: [],
      adjectives: [],
    };
  },
  methods: {
    getWords() {
      this.getMeaningLikes();
      this.getRhymesWiths();
      this.getAdjectives();
    },
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
      this.gifs = json.data.map(gif => ({
        src: `https://media.giphy.com/media/${gif.id}/giphy.gif`,
      }));
    },
    fetchDatamuse(query, bucket) {
      this.gifs = [];
      const searchEndPoint = 'https://api.datamuse.com';
      const limit = 10;
      const proxyUrl = 'https://cors-anywhere.herokuapp.com/';
      const url = `${searchEndPoint}${query}${this.searchTerm}&max=${limit}`;

      this.getGifs(this.searchTerm);

      fetch(proxyUrl + url)
        .then(response => response.json())
        .then((json) => {
          this[`${bucket}`] = json;
        })
        .catch((err) => {
          console.log(err);
        });
    },
    async getMeaningLikes() {
      await this.fetchDatamuse('/words?ml=', 'words');
    },
    async getRhymesWiths() {
      await this.fetchDatamuse('/words?rel_rhy=', 'rhymesWiths');
    },
    async getAdjectives() {
      await this.fetchDatamuse('/words?rel_jjb=', 'adjectives');
    },
  },
};
</script>

<style scoped lang="scss">
  .search-input {
    padding: 10px 20px;
    margin: 0 auto;
    font-size: 18px;
    border-radius: 5px;
  }
  .search-input__button {
    transition: all .4s ease;
    background-color: purple;
    font-size:18px;
    color: white;
    padding: 12px 20px;
    width: 100px;
    border: none;
    border-radius: 5px;
  }
  .search-input__container {
    width: 500px;
    margin: 0 auto;
  }
  .search-input__button:hover {
    background-color: lighten(purple, 5);
  }

  .gif-container {
    margin-top: 30px;
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(300px,1fr));
    grid-column-gap: 15px;
    grid-row-gap: 15px;
    align-items: center;
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
    margin-top: 30px;
    padding-bottom: 30px;
    background: #ffffff;
    border-radius: 5px;
    display: grid;
    grid-template-rows: 70px 1fr;
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
    background-color: purple;
  }
  .word-container__item--text {
    font-size: 20px;
    width: 400px;
  }
  .word-container__item--header {
    font-size: 22px;
    justify-self: center;
  }
</style>
