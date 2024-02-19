<template>
  <infoPokemon v-if="isOpen" @modalStatus="isOpen = false" :pokemon="pokemonSelecionado"/>

  
  <div class="flex flex-col items-center bg-[#f0f0f0] h-auto min-h-screen" :class="{'travarScroll' : isOpen}">
    <div class="flex justify-center items-center w-full max-h-[50px] bg-[#f7f7f7] shadow-md sticky top-0" :class="{hidden : isOpen}">
      <img src="https://cdn.icon-icons.com/icons2/2248/PNG/512/pokeball_icon_136305.png" class=" max-w-10 max-h-10" :class="{hidden : isOpen}">
    </div>
    <div v-if="isLoading" class="flex h-screen justify-center items-center">
          <div  class="flex justify-center items-center animate-spin border-t-2 border-blue-700 border-solid rounded-full w-16 h-16"></div>
      </div>
    <div class="w-full max-w-[1000px] flex flex-col items-center" v-if="!isLoading">
      <div class="flex justify-center items-center m-4 h-auto w-full sticky top-1" >
        <input type="text" placeholder="Pesquisar Pokémon" class="w-[80%] text-center rounded-lg p-2 shadow" :class="{hidden : isOpen}" v-model="pesquisa"/>
      </div>
      <div class="flex justify-center items-center">
        <div class="flex flex-wrap justify-center h-auto min-h-full gap-3">
          <div  v-if="pesquisa === ''"  v-for="(pokemon, i) in pokemons" :key="i" @click="abrirModal(pokemon)" :class="pokemon.color, pokemon.hover" class="flex justify-center items-center flex-col rounded-lg min-h-[220px] h-auto w-[150px] md:w-[190px] md:min-h-[200px] shadow p-2">
              <img :src="pokemon.image" class="max-w-[120px] sm:max-w-[150px] md:max-w-[120px]"> 
              <p class="text-lg lg:text-xl text-black text-center">#{{ pokemon.id }} {{ pokemon.name }}</p>
            </div>
          <div v-if="pesquisa !== ''" v-for="(pokemon, i) in filtroPokemon" :key="i" @click="abrirModal(pokemon)" :class="pokemon.color, pokemon.hover" class="flex justify-center items-center flex-col rounded-lg min-h-[220px] h-auto w-[150px] md:w-[190px] md:min-h-[200px] shadow p-2">
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


let intervaloCarregamento
let typeColors = {
  water: 'bg-[#c0e2fc]',
  fire: 'bg-[#ffb778]',
  grass: 'bg-[#9bf2a5]',
  flying: 'bg-[#cbe1f2]',
  poison: 'bg-[#e0b3e8]',
  normal: 'bg-[#e0d1b8]',
  fighting: 'bg-[#e3b1b1]',
  psychic: 'bg-[#f5b0cd]',
  ghost: 'bg-[#ac9ab5]',
  dark: 'bg-[#ada0a0]',
  bug: 'bg-[#dbf0a1]',
  fairy: 'bg-[#f7cbe7]',
  steel: 'bg-[#c5c5db]',
  electric: 'bg-[#f7eeb0]',
  ice: 'bg-[#d5f1f5]',
  dragon: 'bg-[#c1c9e0]',
  rock: 'bg-[#dbc19c]',
  ground: 'bg-[#e3bd8a]'
};

let hoverTypeColors = {
  water: 'hover:bg-[#7abcf0]',
  fire: 'hover:bg-[#e07e53]',
  grass: 'hover:bg-[#68d46f]',
  flying: 'hover:bg-[#7babd1]',
  poison: 'hover:bg-[#da8be8]',
  normal: 'hover:bg-[#c9c385]',
  fighting: 'hover:bg-[#d68b8b]',
  psychic: 'hover:bg-[#db86a9]',
  ghost: 'hover:bg-[#9a83c9]',
  dark: 'hover:bg-[#806b6b]',
  bug: 'hover:bg-[#bbd479]',
  fairy: 'hover:bg-[#e396c8]',
  steel: 'hover:bg-[#9d9dd1]',
  electric: 'hover:bg-[#d6be5a]',
  ice: 'hover:bg-[#88d2db]',
  dragon: 'hover:bg-[#567da3]',
  rock: 'hover:bg-[#bdae75]',
  ground: 'hover:bg-[#c49352]'
};

const isOpen = ref(false)
const pokemons = ref([]);
const pesquisa = ref('');
const pokemonSelecionado = ref('')
const isLoading = ref(true);



const carregarPokemons = async (offset, limit) => {
  try {
    const pokemonResponse = await axios.get(`https://pokeapi.co/api/v2/pokemon?offset=${offset}&limit=${limit}`);
    const pokemonCarregados = pokemonResponse.data.results;

    for (const pokemon of pokemonCarregados) {
      const response = await axios.get(pokemon.url);
      const newPokemon = {
        id: response.data.id,
        name: formatarNome(response.data.name),
        image: `https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/other/official-artwork/${response.data.id}.png`,
        gif: `https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/other/showdown/${response.data.id}.gif`,
        type: response.data.types,
        color: typeColors[response.data.types[0].type.name.toLowerCase()],
        hover: hoverTypeColors[response.data.types[0].type.name.toLowerCase()],
        stats: response.data.stats,
        abilities: response.data.abilities        
      }
      
      pokemons.value.push(newPokemon)
    }
  } catch (error) {
    console.error('Erro ao carregar Pokémon:', error.message);
  } finally {
      setTimeout(() => {
      isLoading.value = false;
    }, 150);
  }
};


const formatarNome = (str) => {
  str = str.replace(/-/g, ' ');
  return str.replace(/\b\w/g, match => match.toUpperCase());
}

const carregarOutrosPokemon = () => {
    intervaloCarregamento = setInterval(async () => {
      const offset = pokemons.value.length;
      await carregarPokemons(offset, 50);
    }, 1800);
  };

onMounted(() => {
  carregarOutrosPokemon();
});

onBeforeUnmount(() => {
  clearInterval(intervaloCarregamento);
});

const filtroPokemon = computed(() => {
  return pokemons.value.filter((pokemon) =>
    pokemon.name.toLowerCase().includes(pesquisa.value.toLowerCase())
  );
});

function abrirModal(pokemon) {
  pokemon = JSON.parse(JSON.stringify(pokemon))
  for (let type of pokemon.type){  
    type.type.name = formatarNome(type.type.name)
  } 
  for (let ability of pokemon.abilities){  
    ability.ability.name = formatarNome(ability.ability.name)
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
