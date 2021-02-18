<template>
  <div class="h-100">
    <div class="card-image h-65">
      <img :src="imgUrl" height="100%" width="100%" />
    </div>
    <div class="card-name">
      <p>{{ pokemon.name }}</p>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "CardPokemon",
  props: ["pokemon"],
  data() {
    return {
      pokemonId: this.pokemon.url.split("/")[6],
      imgUrl: "",
    };
  },
  mounted() {
    axios
      .get(this.pokemon.url)
      .then(
        (response) =>
          (this.imgUrl =
            response.data.sprites.other["official-artwork"].front_default)
      )
      .catch((error) => console.log(error));
  },
};
</script>

<style>
</style>