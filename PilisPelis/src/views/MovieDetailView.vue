<template>
  <div v-if="movie" class="movie">
    <button @click="goBack" class="back-button">
      Regresar
    </button>
    <h1 class="movie-title">{{ movie.title }} ({{ getReleaseYear(movie.release_date) }})</h1>
    <p><strong>Director:</strong> {{ movie.director }}</p>
    <img class="movie-poster" :src="`https://image.tmdb.org/t/p/w500/${movie.poster_path}`" :alt="movie.title" />
    <p class="movie-overview">{{ movie.overview }}</p>
    <button @click="toggleFavorite" class="favorite-button">
      {{ isFavorite ? 'Quitar de Favoritos' : 'Agregar a Favoritos' }}
    </button>
  </div>
  <div v-else>
    <p>Cargando...</p>
  </div>
</template>

<script>
export default {
  name: 'MovieDetailView',
  props: {
    id: {
      type: String,
      required: true,
    },
  },
  data() {
    return {
      movie: null,
      isFavorite: false,
    };
  },
  async created() {
    await this.fetchMovieDetails();
    this.loadFavoriteStatus();
  },
  methods: {
    async fetchMovieDetails() {
      try {
        const apiKey = '3236d661dc35f69546bb2df1a55dd0ac';
        const response = await fetch(`https://api.themoviedb.org/3/movie/${this.id}?api_key=${apiKey}&append_to_response=credits`);

        if (!response.ok) {
          throw new Error('Error fetching movie details');
        }

        const data = await response.json();
        this.movie = {
          ...data,
          director: this.getDirector(data.credits.crew),
        };
      } catch (error) {
        console.error('Error fetching movie details:', error.message);
      }
    },
    loadFavoriteStatus() {
      const favorites = JSON.parse(localStorage.getItem('favoriteMovies')) || [];
      this.isFavorite = favorites.some(fav => fav.id === this.movie.id);
    },
    toggleFavorite() {
      this.isFavorite = !this.isFavorite;

      if (this.isFavorite) {
        this.addToFavorites();
      } else {
        this.removeFromFavorites();
      }
    },
    addToFavorites() {
      let favorites = JSON.parse(localStorage.getItem('favoriteMovies')) || [];
      favorites.push(this.movie);
      localStorage.setItem('favoriteMovies', JSON.stringify(favorites));
    },
    removeFromFavorites() {
      let favorites = JSON.parse(localStorage.getItem('favoriteMovies')) || [];
      favorites = favorites.filter(fav => fav.id !== this.movie.id);
      localStorage.setItem('favoriteMovies', JSON.stringify(favorites));
    },
    goBack() {
      this.$router.go(-1);
    },
    getReleaseYear(dateString) {
      if (!dateString) return '';
      return new Date(dateString).getFullYear();
    },
    getDirector(crew) {
      if (!crew) return '';
      const director = crew.find(member => member.job === 'Director');
      return director ? director.name : '';
    },
  },
};
</script>

<style scoped>
.back-button {
  height: 5%;
  width: 20%;
  display: flex;
  align-items: center;
  font-size: 16px;
  color: #fff;
  background-color: #007bff;
  border: none;
  border-radius: 5px;
  padding: 10px;
  cursor: pointer;
  margin-bottom: 20px;
}

.back-button:hover {
  background-color: #0056b3;
}

.movie {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
  background-color: #f9f9f9;
  border-radius: 8px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

.movie-title {
  font-size: 28px;
  margin-bottom: 10px;
  text-align: center;
}

.movie-poster {
  display: block;
  margin: 0 auto;
  max-width: 50%;
  border-radius: 8px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

.movie-overview {
  margin-top: 20px;
  line-height: 1.6;
  text-align: justify;
}

.favorite-button {
  display: block;
  margin: 20px auto;
  padding: 10px 20px;
  font-size: 16px;
  color: #fff;
  background-color: #28a745;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.favorite-button:hover {
  background-color: #218838;
}
</style>