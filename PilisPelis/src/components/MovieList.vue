<template>
  <div class="movie-list">
    <MovieCard v-for="movie in movies" :key="movie.id" :movie="movie" @selectMovie="selectMovie(movie)" />
  </div>
</template>

<script>
import MovieCard from './MovieCard.vue';

export default {
  name: 'MovieList',
  components: {
    MovieCard,
  },
  props: {
    movies: {
      type: Array,
      required: true,
    },
  },
  methods: {
    selectMovie(movie) {
      this.$emit('movieSelected', movie);
    },
  },
};
</script>


