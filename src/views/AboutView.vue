<template>
  <div class="about">
    <div v-if="pokemon"
    class="w-3/12 m-auto bg-purple-100 shadow-2xl flex flex justify-center flex-col items-center">
    <h3 class="text-2xl text-green-900 uppercase">{{pokemon.name}}</h3>
    <div class="flex justify-center">
      <img class="w-48" :src="pokemon.sprites.front_shiny" alt="">
      <img class="w-48" :src="pokemon.sprites.back_shiny" alt="">
    </div>
    <h3 class="text-yellow-400">Types</h3>
    <div v-for="(type,idx) in pokemon.types" :key="idx">
      <h5 class="text-blue-900">{{type.type.name}}</h5>
    </div>
    <div class="flex flex-col justify-center">
      <form>
        <div class="mt-5">
          <label>Love this pokemon?</label>
          <br>
          <input type="radio" value="Like" v-model="choice" class="mt-3"/> Like
          <input type="radio" value="Dislike" v-model="choice" class="mt-3"/> Dislike
        </div>
        <button class="bg-blue-500 hover:bg-blue-700 text-white font-bol py-2 px-4 rounded-full my-4" type="button" @click="vote">Vote</button>
        <br>
      </form>
      <div>
        <h1>result</h1>
        <p v-for="(value, key) of results" :key="key">{{ key }}: {{ value }}</p>
      </div>
    </div>
    </div>
  </div>
</template>

<script>
import { useRoute } from "vue-router";
import { reactive, toRefs } from "vue";

export default {
  data(){
    return {
      choice: "",
      results:{},
    };
  },
  methods: {
    vote() {
      if (!localStorage.getItem("vote-result")) {
        localStorage.setItem("vote-result", JSON.stringify({}));
      }
      const { choice } = this;
      const results = JSON.parse(localStorage.getItem("vote-result"));
      if (!Object.prototype.hasOwnProperty.call(results, choice)) {
        results[choice] = 0;
      }
      results[choice]++;
      localStorage.setItem("vote-result", JSON.stringify(results));
      this.results = JSON.parse(localStorage.getItem("vote-result"));
    },
  },
  setup(){
    const route = useRoute();
    const state = reactive({
      pokemon: null
    })

    fetch(`https://pokeapi.co/api/v2/pokemon/${route.params.slug}/`)
      .then((res)=> res.json())
      .then((data)=>{
        console.log(data)
        state.pokemon = data
      })

      return {... toRefs(state)}
  }
}
</script>