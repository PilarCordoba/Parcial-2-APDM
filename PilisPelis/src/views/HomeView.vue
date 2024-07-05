<template>
  <div class="busqueda">
    <input v-model="searchQuery" @input="searchMovies" placeholder="Buscar película..." />
    <button class="btnBuscar" @click="searchMovies">Buscar</button>
    <h2>Filtrar por Género</h2>
    <select v-model="selectedGenre" @change="filterMoviesByGenre">
      <option value="">Todos</option>
      <option v-for="genre in genres" :key="genre.id" :value="genre.id">{{ genre.name }}</option>
    </select>
    
    <MovieList :movies="movies" @movieSelected="handleMovieSelected" />
    <h2>Películas Populares</h2>
    <MovieList :movies="popularMovies" @movieSelected="handleMovieSelected" />
  </div>
</template>

<script>
import MovieList from '../components/MovieList.vue';

export default {
  name: 'HomeView',
  components: {
    MovieList,
  },
  data() {
    return {
      searchQuery: '',
      movies: [], // Variable para almacenar las películas a mostrar
      selectedMovie: null,
      popularMovies: [],
      genres: [], // Arreglo para almacenar los géneros disponibles
      selectedGenre: '', // Variable para almacenar el género seleccionado
    };
  },
  methods: {
    async searchMovies() {
      if (this.searchQuery.trim() === '') {
        this.movies = [];
        return;
      }

      const apiKey = '3236d661dc35f69546bb2df1a55dd0ac';
      const response = await fetch(`https://api.themoviedb.org/3/search/movie?query=${this.searchQuery}&api_key=${apiKey}`);

      if (!response.ok) {
        console.error('Error searching movies:', response.statusText);
        return;
      }

      const data = await response.json();
      this.movies = data.results;
    },
    async fetchGenres() {
      const apiKey = '3236d661dc35f69546bb2df1a55dd0ac';
      const response = await fetch(`https://api.themoviedb.org/3/genre/movie/list?api_key=${apiKey}`);

      if (!response.ok) {
        console.error('Error fetching genres:', response.statusText);
        return;
      }

      const data = await response.json();
      this.genres = data.genres;
    },
    async filterMoviesByGenre() {
      // Verificar si no se ha seleccionado ningún género
      if (!this.selectedGenre) {
        this.movies = [...this.filteredMovies]; // Mostrar todas las películas
        return;
      }

      try {
        const apiKey = '3236d661dc35f69546bb2df1a55dd0ac';
        const response = await fetch(`https://api.themoviedb.org/3/discover/movie?api_key=${apiKey}&with_genres=${this.selectedGenre}`);
        
        if (!response.ok) {
          console.error('Error fetching movies by genre:', response.statusText);
          return;
        }

        const data = await response.json();
        this.movies = data.results;
      } catch (error) {
        console.error('Error filtering movies by genre:', error);
      }
    },
    handleMovieSelected(movie) {
      this.$router.push({ name: 'MovieDetail', params: { id: movie.id } });
    },
    async fetchPopularMovies() {
      const apiKey = '3236d661dc35f69546bb2df1a55dd0ac';
      const response = await fetch(`https://api.themoviedb.org/3/movie/popular?api_key=${apiKey}&language=en-US&page=1`);
      const data = await response.json();
      this.popularMovies = data.results;
    }
  },
  mounted() {
    this.fetchPopularMovies();
    this.fetchGenres();
  },
};
</script>
<style scoped>
/* Estilos opcionales para el botón de búsqueda y otros elementos */
.btnBuscar {
  margin-left: 1rem;
  padding: 0.5rem 1rem;
  font-size: 1rem;
  cursor: pointer;
}

select {
  margin-left: 1rem;
  padding: 0.5rem;
  font-size: 1rem;
}
</style>