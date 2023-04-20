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
          <a :href="show.url" target="_blank" class="read-more" tag="button" type="button" v-bind:style="{opacity: 1}" v-show="show.url">Read More</a>
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
  name: 'App',
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



<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

nav {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1rem;
  background-color: #6dace7;
  color: #fff;
}

.logo h1 {
  font-size: 2rem;
}

.menu {
  list-style: none;
  margin: 0;
  padding: 0;
  display: flex;
}

.menu li {
  margin-right: 1rem;
}

.menu form {
  display: flex;
}

.menu input[type="text"] {
  padding: 0.5rem;
  border-radius: 0.25rem;
  border: none;
  margin-right: 0.5rem;
}

.menu button[type="submit"] {
  padding: 0.5rem;
  border-radius: 0.25rem;
  border: none;
  background-color: #fff;
  color: #9ecdef;
  font-weight: bold;
}

.container {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 2rem;
  padding: 2rem;
}

.card {
  display: flex;
  flex-direction: column;
  margin: 20px;
  background-color: #fff;
  border-radius: 5px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.2);
  transition: all 0.3s ease-in-out;
}

.card:hover {
  box-shadow: 0 0 20px rgba(0, 0, 0, 0.3);
  transform: translateY(-5px);
}

.card-image {
  position: relative;
  height: 300px;
  overflow: hidden;
  border-top-left-radius: 5px;
  border-top-right-radius: 5px;
}

.card-image img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  transition: all 0.3s ease-in-out;
}

.card-image:hover img {
  transform: scale(1.1);
}

.card-content {
  padding: 20px;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  flex-grow: 1;
}

.card-content h2 {
  font-size: 24px;
  margin-bottom: 10px;
}

.card-content p {
  font-size: 16px;
  line-height: 1.5;
  margin-bottom: 10px;
}

.card-content a {
  align-self: flex-end;
  margin-top: auto;
  color: #0077FF;
  font-weight: bold;
  transition: all 0.3s ease-in-out;
}

.card-content a:hover {
  transform: scale(1.1);
  color: #0055FF;
}

nav {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1rem;
  background-color: #94c2f3;
  color: #fff;
}

nav ul {
  list-style: none;
  margin: 0;
  padding: 0;
  display: flex;
}

nav ul li {
  margin-right: 1rem;
}

nav ul li:last-child {
  margin-right: 0;
}

nav ul li a {
  color: #fff;
  text-decoration: none;
  font-weight: bold;
}

/* Style for Search Engine */
nav form input[type="text"] {
  padding: 0.5rem;
  border-radius: 0.25rem;
  border: none;
}

nav form button[type="submit"] {
  padding: 0.5rem;
  border-radius: 0.25rem;
  border: none;
  background-color: #bb7f7f;
  color: #d6e2ef;
  font-weight: bold;
}

/* Style for Home Page */
.home {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background-color: #eee;
}

.home h1 {
  font-size: 3rem;
  margin-bottom: 2rem;
}

/* Style for Movie List Page */
.movies {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
  gap: 1rem;
  padding: 1rem;
  background-color: #4daaec;
}

.movies h2 {
  font-size: 1.5rem;
  margin-bottom: 0.5rem;
}

.movies img {
  width: 100%;
  height: auto;
}

/* Responsive Design */
@media (max-width: 768px) {
  nav {
    flex-direction: column;
    align-items: flex-start;
  }

  nav ul {
    margin-top: 1rem;
  }

  nav ul li {
    margin-right: 0;
    margin-bottom: 0.5rem;
  }

  nav form input[type="text"],
  nav form button[type="submit"] {
    width: 100%;
  }

  .home h1 {
    font-size: 2rem;
    margin-bottom: 1rem;
  }

  .movies {
    grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
    gap: 0.5rem;
    padding: 0.5rem;
  }


  .movies h2 {
    font-size: 1.25rem;
    margin-bottom: 0.25rem;
  }
}
</style>
