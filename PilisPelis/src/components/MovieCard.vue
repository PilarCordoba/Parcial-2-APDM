<template>
  <div class="movie-card" @click="handleClick">
    <img class="movie-poster" :src="`https://image.tmdb.org/t/p/w500/${movie.poster_path}`" :alt="movie.title" />
    <h3 class="movie-title" >{{ movie.title }}</h3>
  </div>
</template>

<script>
export default {
  name: 'MovieCard',
  props: {
    movie: {
      type: Object,
      required: true,
    },
  },
  methods: {
    handleClick() {
      this.$emit('selectMovie', this.movie);
    },
  },
};
</script>
<style scoped>
.movie-card {
  border: 1px solid #ccc;
  border-radius: 8px;
  padding: 10px;
  cursor: pointer;
  transition: transform 0.3s ease;
  width: 200px; /* Ajusta el ancho según sea necesario */
  margin: 10px; /* Ajusta el margen entre tarjetas */
}

.movie-card:hover {
  transform: translateY(-5px);
}

.movie-poster {
  width: 100%;
  border-radius: 8px;
}

.movie-title {
  margin-top: 10px;
  font-size: 16px;
  text-align: center;
}
</style>
