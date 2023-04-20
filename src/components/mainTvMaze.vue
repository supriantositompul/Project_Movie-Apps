<template>
  <div id="app">
    <nav>
      <div class="logo">
        <h1>Movie App</h1>
      </div>
      <ul class="menu">
        <li><a href="#">Home</a></li>
        <li><a href="#">Movie List</a></li>
        <li>
          <form @submit.prevent="searchShows">
            <input type="text" v-model="searchTerm" placeholder="Search Movies...">
            <button type="submit">Search</button>
          </form>
        </li>
      </ul>
    </nav>
    <div class="container">
      <div v-for="(show, index) in shows" :key="index" class="card">
        <div class="card-image">
          <img v-if="show.image" :src="show.image.medium" alt="poster">
        </div>

        <div class="card-content">
          <h2>{{ show.name }}</h2>
          <p>{{ show.summary }}</p>
          <a :href="show.url" target="_blank" class="read-more" tag="button" type="button" v-bind:style="{opacity: 1}" v-show="show.url">View More</a>
        </div>
      </div>
      <div v-if="loading">Loading...</div>
      <div v-if="!loading && shows.length === 0">No results found.</div>
      <div v-if="shows.length > 0">
        <button v-if="page > 1" @click="prevPage">Previous Page</button>
        <button v-if="page * pageSize < shows.length" @click="nextPage">Next Page</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      shows: [],
      loading: true,
      error: null,
      searchTerm: '',
      page: 1,
      pageSize: 10,
      filteredShows: [],
      totalPages: 1,
      currentShows: [],
    };
  },
  created() {
    this.getShows();
  },
  methods: {
    async getShows() {
      try {
        const response = await fetch('https://api.tvmaze.com/shows');
        const data = await response.json();
        this.shows = data.map(show => {
          return {
            id: show.id,
            name: show.name,
            image: show.image,
            url: show.url // tambahkan properti url
          };
        });
        this.loading = false;
        this.filteredShows = [...this.shows];
        this.totalPages = Math.ceil(this.filteredShows.length / this.pageSize);
        this.paginateShows();
      } catch (error) {
        this.error = error.message;
        console.error(error);
      }
    },
    async searchShows() {
      try {
        const response = await fetch(`https://api.tvmaze.com/search/shows?q=${this.searchTerm}`);
        const data = await response.json();
        this.shows = data.map(result => {
          return {
            id: result.show.id,
            name: result.show.name,
            image: result.show.image,
            url: result.show.url
          };
        });
        this.loading = false;
        this.filteredShows = [...this.shows];
        this.totalPages = Math.ceil(this.filteredShows.length / this.pageSize);
        this.paginateShows();
      } catch (error) {
        this.error = error.message;
        console.error(error);
      }
    },
    prevPage() {
      this.page--;
      this.paginateShows();
    },
    nextPage() {
      this.page++;
      this.paginateShows();
    },
    paginateShows() {
      const startIndex = (this.page - 1) * this.pageSize;
      const endIndex = startIndex + this.pageSize;
      this.currentShows = this.filteredShows.slice(startIndex, endIndex);
    },
  },
};
</script>
