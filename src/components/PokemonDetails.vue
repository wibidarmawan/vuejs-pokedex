<template>
  <div class="ml-1 my-1 mr-3">
    <div class="row d-flex justify-content-center">
      <img
        :src="imgUrl"
        alt=""
        width="50%"
      />
    </div>
    <div class="row">
      <div class="col pokemon-name">
        <h1 class="text-center">{{name}}</h1>
        <div class="property">
          <div class="left">Base Experience</div>
          <div class="right">{{ baseExp }}XP</div>
        </div>
        <div class="property">
          <div class="left">Height</div>
          <div class="right">{{ height }}m</div>
        </div>
        <div class="property">
          <div class="left">Weight</div>
          <div class="right">{{ weight }}kg</div>
        </div>
        <h3 class="types mt-3">Pokemon Types</h3>
        <div class="d-flex">
          <div class="type mr-3 py-1 px-3 bg-warning" v-for="(value, index) in pokemonType" :key="'value' + index">
            {{ value.type.name }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "PokemonDetails",
  props: ["selected"],
  data() {
    return {
      url: '',
      imgUrl: '',
      name: '',
      baseExp: '',
      height: '',
      weight: '',
      pokemonType: []
    };
  },
  methods: {
    fetchData(data){
      this.imgUrl = data.sprites.other['official-artwork'].front_default,
      this.name = data.name,
      this.baseExp = data.base_experience,
      this.height = data.height/10,
      this.weight = data.weight/10,
      this.pokemonType = data.types
    }
  },
  watch: {
    selected:function(val){
      if(val){
        this.url = val;
      }
    },
    url: function (val) {
      if (val) {
        axios
          .get(this.url)
          .then((response) => this.fetchData(response.data))
          .catch((error) => console.log(error));
      }
    },
  },
  mounted(){
    this.url = this.selected;
  }
};
</script>

<style>
</style>