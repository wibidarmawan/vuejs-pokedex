<template>
  <div class="container-fluid">
    <Navbar />
    <div class="row mb-2">
      <div class="container">
        <input
          v-model="search"
          type="text"
          class="form-control border border-dark"
          placeholder="Search Pokemon in this page"
        />
      </div>
    </div>
    <div class="row content">
      <div
        class="col-md-8 d-flex flex-wrap text-center justify-content-center "
        width="100%"
      >
        <div class="row d-flex justify-content-center h-100 w-100 border align-items-start">
          <div
            class="col-md-1 pokemon-card pokemon-name p-0 m-1 shadow btn flex-wrap border border-dark"
            @click="showDetails(pokemon.url)"
            v-for="pokemon in resultQuery"
            :key="pokemon.name"
          >
            <CardPokemon :pokemon="pokemon" />
          </div>
        </div>
      </div>
      <div class="col-md-4">
        <PokemonDetails v-if="show" :selected="selected" />
      </div>
    </div>
    <div class="row justify-content-center mt-2">
      <div class="row justify-content-center align-items-center">
          <div class="col-md-3 m-0">
            <span
              class="btn input-group-text justify-content-center"
              v-bind:class="{ disabled: previous }"
              @click="previousPage(previousUrl)"
              ><b-icon-arrow-left></b-icon-arrow-left>
            </span>
          </div>
          <div class="col-md-3 m-0">
            <b-form-input
              v-model="pageNumber"
              type="text"
              class="form-control text-center"
              :value="pageNumber"
              @keyup.enter="gotoPage"
              @focus="$event.target.select()"
            />
          </div>
          <div class="col-md-3 m-0">
            <span
              class="btn input-group-text justify-content-center"
              v-bind:class="{ disabled: next }"
              @click="nextPage(nextUrl)"
              ><b-icon-arrow-right></b-icon-arrow-right
            ></span>
          </div>
        </div>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import Navbar from "@/components/Navbar.vue";
import PokemonDetails from "@/components/PokemonDetails.vue";
import CardPokemon from "@/components/CardPokemon.vue";
import axios from "axios";

export default {
  name: "Home",
  components: {
    Navbar,
    PokemonDetails,
    CardPokemon,
  },

  data() {
    return {
      pokemons: [],
      allPokemons: [],
      allP:[],
      search: "",
      filteredPokemon: [],
      selected: "",
      show: false,
      previous: true,
      next: false,
      previousUrl: "",
      nextUrl: "",
      pageNumber: 0,
      offset: 0,
      cek: 0
    };
  },

  computed:{
    resultQuery(){
      if(this.search){
        return this.pokemons.filter((item)=>{
          return item.name.toLowerCase().includes(this.search.toLowerCase())
        })
      } else{
        return this.pokemons
      }
    }
  },

  methods: {
    fetchData(data) {
      this.pokemons = data.results;
      this.checkPage(data);
      // this.allP.forEach(element => {
      //   this.allPokemons.push(element);
      // });
    },

    // fetchAllPokemon() {
    //   axios
    //     .get("https://pokeapi.co/api/v2/pokemon/?limit=1180")
    //     .then((response) => this.allP = response.data.results)
    //     .catch((error) => console.log(error));
    // },

    checkPage(data) {
      if (data.previous != null) {
        this.previous = false;
        this.previousUrl = data.previous;
        if (this.previousUrl.search("offset") != -1) {
          this.offset = parseInt(
            this.previousUrl.slice(
              this.previousUrl.search("offset") + 7,
              this.previousUrl.search("&")
            )
          );
          this.pageNumber = Math.round((this.offset + 40) / 40) + 1;
        }
      } else {
        this.previous = true;
        this.pageNumber = 1;
      }
      if (data.next != null) {
        this.next = false;
        this.nextUrl = data.next;
      } else {
        this.next = true;
      }
    },

    showDetails(url) {
      this.selected = url;
      this.show = true;
    },
    previousPage(url) {
      if (!this.previous) {
        url = url.split("&")[0] + "&limit=40";
        axios
          .get(url)
          .then((response) => this.fetchData(response.data))
          .catch((error) => console.log(error));
      }
    },
    nextPage(url) {
      if (!this.next) {
        url = url.split("&")[0] + "&limit=40";
        axios
          .get(url)
          .then((response) => this.fetchData(response.data))
          .catch((error) => console.log(error));
      }
    },
    gotoPage() {
      this.pageNumber = parseInt(this.pageNumber);
      if (this.pageNumber <= 28 && this.pageNumber >= 1) {
        axios
          .get(
            "https://pokeapi.co/api/v2/pokemon/?offset=" +
              (this.pageNumber - 1) * 40 +
              "&limit=40"
          )
          .then((response) => this.fetchData(response.data))
          .catch((error) => console.log(error));
      } else if (this.pageNumber > 28) {
        this.pageNumber = 28;
        axios
          .get("https://pokeapi.co/api/v2/pokemon/?offset=1080&limit=40")
          .then((response) => this.fetchData(response.data))
          .catch((error) => console.log(error));
      } else {
        this.pageNumber = 1;
        axios
          .get("https://pokeapi.co/api/v2/pokemon/?offset=0&limit=40")
          .then((response) => this.fetchData(response.data))
          .catch((error) => console.log(error));
      }
    },
  },

  mounted() {
    // this.fetchAllPokemon();
    axios
      .get("https://pokeapi.co/api/v2/pokemon/?offset=0&limit=40")
      .then((response) => this.fetchData(response.data))
      .catch((error) => console.log(error));
  },
};
</script>
