<template>
  <infoPokemon v-if="isOpen" @modalStatus="isOpen = false" :pokemon="pokemonSelecionado"/>
  <div class="flex flex-col items-center bg-[#fcfcfc] h-auto min-h-screen" :class="{'travarScroll' : isOpen}">
    <div class="flex justify-center items-center w-full h-[50px] bg-[#fff] shadow-md sticky top-0" :class="{hidden : isOpen}">
      <img src="https://cdn.icon-icons.com/icons2/2248/PNG/512/pokeball_icon_136305.png" class=" w-10 h-10" :class="{hidden : isOpen}">
    </div>
    <div class="w-full max-w-[1000px] flex flex-col items-center">
      <div class="flex justify-center items-center m-4 h-auto w-full sticky top-2" >
        <input type="text" placeholder="Pesquisar Pokémon" class="w-[80%] text-center rounded-lg p-2 shadow" :class="{hidden : isOpen}" v-model="pesquisa"/>
      </div>
      <div class="flex justify-center items-center">
        <div class="flex flex-wrap justify-center h-auto min-h-full text-white gap-3">
          <div v-if="pesquisa === ''" v-for="(pokemon, i) in pokemons" :key="i" @click="openModal(pokemon)" class="flex justify-center items-center flex-col bg-[#dedede]/50 rounded-lg min-h-[220px] h-auto w-[150px] md:w-[190px] md:min-h-[200px] shadow hover:bg-[#c4c4c4] p-2">
            <img :src="pokemon.image" class="max-w-[120px] sm:max-w-[150px] md:max-w-[120px]"> 
            <p class="text-lg lg:text-xl text-black text-center">#{{ pokemon.id }} {{ pokemon.name }}</p>    
          </div>
          <div v-if="pesquisa !== ''" v-for="(pokemon, i) in filtroPokemon" :key="i" @click="openModal(pokemon)" class="flex justify-center items-center flex-col bg-[#dedede]/50 rounded-lg min-h-[220px] h-auto w-[150px] md:w-[190px] md:min-h-[200px] shadow hover:bg-[#c4c4c4] p-2">
            <img :src="pokemon.image" class="max-w-[120px] sm:max-w-[150px] md:max-w-[120px]">
            <p class="text-lg lg:text-xl text-black text-center">#{{ pokemon.id }} {{ pokemon.name }}</p>    
          </div>
        </div> 
      </div>
    </div>
  </div>
</template>

<script setup>
import infoPokemon from '@/components/infoPokemon.vue'
import { ref, onMounted, onBeforeUnmount, computed } from 'vue';
import axios from 'axios';

let loadInterval;
const isOpen = ref(false)
const pokemons = ref([]);
const pesquisa = ref('');
const pokemonSelecionado = ref('')

const carregarPokemons = async (offset, limit) => {
  try {
    const pokemonResponse = await axios.get(`https://pokeapi.co/api/v2/pokemon?offset=${offset}&limit=${limit}`);
    const loadedPokemon = pokemonResponse.data.results;

    for (const pokemon of loadedPokemon) {
      const response = await axios.get(pokemon.url);
      const newPokemon = {
        id: response.data.id,
        name: formatName(response.data.name),
        image: `https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/other/official-artwork/${response.data.id}.png`,
        gif: `https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/other/showdown/${response.data.id}.gif`,
        // type: response.data.types.map((type) => formatName(type.type.name)), 
        type: response.data.types,
        stats: response.data.stats,
        // abilities: response.data.abilities.map((ability) => formatName(ability.ability.name))
        abilities: response.data.abilities        
      }
      pokemons.value.push(newPokemon)
    }
  } catch (error) {
    console.error('Erro ao carregar Pokémon:', error.message);
  }
};

const formatName = (str) => {
  str = str.replace(/-/g, ' ');
  return str.replace(/\b\w/g, match => match.toUpperCase());
}

const carregarOutrosPokemon = () => {
    loadInterval = setInterval(async () => {
      const offset = pokemons.value.length;
      await carregarPokemons(offset, 50);
    }, 2000);
  };

onMounted(() => {
  carregarOutrosPokemon();
});

onBeforeUnmount(() => {
  clearInterval(loadInterval);
});

const filtroPokemon = computed(() => {
  return pokemons.value.filter((pokemon) =>
    pokemon.name.toLowerCase().includes(pesquisa.value.toLowerCase())
  );
});

function openModal(pokemon) {
  pokemon = JSON.parse(JSON.stringify(pokemon))
  for (let type of pokemon.type){  
    type.type.name = formatName(type.type.name)
  } 
  for (let ability of pokemon.abilities){  
    ability.ability.name = formatName(ability.ability.name)
  } 
  pokemonSelecionado.value = pokemon
  isOpen.value = true
  travarScroll()
}

function travarScroll(){
    var x=window.scrollX;
    var y=window.scrollY;
    window.onscroll=function(){window.scrollTo(x, y);};
}
</script>

<style>
  
</style>

