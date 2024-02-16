<template>
<div class="fixed inset-0 h-screen w-screen flex justify-center items-center bg-black/65 font-sans" @click="closeModal" :class="getTypeColorBG(pokemon.type[0].type.name)">
    <div class="bg-gradient-to-t w-[95%] h-[85%] rounded-xl max-w-[430px] max-h-[600px] min-w-[300px] flex justify-center items-center shadow-2xl">
        <div class="flex flex-col justify-center w-[92%] h-full">
            <div class="flex justify-around items-center w-full">
                <div class="flex justify-center"><img :src="pokemon.gif" class="min-w-[50px]">
                </div>
                <div class="flex flex-col text-center justify-center items-center">
                    <div class="mb-4">
                        <p class="text-lg tracking-tighter lg: text-xl"> #{{ pokemon.id }} </p>
                        <p class="text-2xl font-black lg:text-3xl"> {{ pokemon.name }}</p>
                    </div>
                    <div v-for="typePokemon in pokemon.type" class="lg:h-8 lg:w-28 shadow rounded-xl h-7 w-24 mb-2 flex items-center justify-center" :style="{ background: getTypeColor(typePokemon.type.name) }">
                        <p class="text-white font-semibold lg:text-lg">{{ typePokemon.type.name }}</p>
                    </div>
                </div>
            </div>
            <div class="my-3">
                <div class="w-full flex justify-center"> <h2 class="text-xl lg:text-lg font-semibold">Abilities</h2> </div>
                <div class="w-full flex flex-wrap justify-center gap-x-5"> <p class="lg:text-md text-center" v-for="ability in pokemon.abilities"> {{ ability.ability.name }} </p> </div>  
            </div>
            <div class="flex flex-col gap-3">
                <div v-for="stat in pokemon.stats" :key="stat.stat.name" class=" w-full">
                <div class="flex items-center w-full">
                    <div class="w-[20%] max-w-[65px] mr-2 ">
                        <p class="text-lg lg:text-xl">{{ statName[stat.stat.name] }} </p>
                    </div>
                    <div>
                        <div :style="{ width: (stat.base_stat)*1.2 + 'px', background: getTypeColor(pokemon.type[0].type.name) }"  class="statBar h-auto lg:h-7 max-w-[210px] sm:max-w-[240px] md:max-w-[280px] lg:max-w-[300px] min-w-[40px] flex justify-end rounded rounded-r-lg">
                            <p class="text-white mr-2 text-sm lg:text-lg">{{ stat.base_stat }}</p>
                        </div>
                    </div>
                </div>  
            </div>
            <div class="flex justify-center">
                <p class="text-black text-center mr-2 lg:text-lg">TOTAL </p>
                <p class="text-center mr-2 lg:text-lg font-semibold" :style="{ color: getTypeColor(pokemon.type[0].type.name) }">{{ totalStats }}</p>
            </div>
            </div>
        </div>    
    </div> 
</div> 
</template>

<script setup>
import { defineEmits, defineProps, onMounted } from 'vue'

const statName = {hp:'HP', attack:'Atk', defense:'Def', 'special-attack':'Sp.Atk', 'special-defense': 'Sp.Def', speed:'Spe'}

const props = defineProps(['pokemon'])
const emit = defineEmits(['modalStatus'])

const totalStats = props.pokemon.stats.reduce((acm, stat) => acm + stat.base_stat, 0)

function closeModal() {
  emit('modalStatus', false)
  destravarScroll()
}

const getTypeColor = (type) =>{
    switch(type.toLowerCase()){
        case 'water':
            return '#2196F3'
        case 'fire':
            return '#E86513' 
        case 'grass':
            return '#4CB050'    
        case 'flying':
            return '#9E87E1'
        case 'poison':
            return '#AA47BC'
        case 'normal':
            return '#BFB97F'
        case 'fighting':
            return '#D32F2E'
        case 'psychic':
            return '#EC407A'
        case 'ghost':
            return '#7556A4'
        case 'dark':
            return '#5D4038'
        case 'bug':
            return '#98B92E'
        case 'fairy':
            return '#F483BB'
        case 'steel':
            return '#B4B4CC'
        case 'electric':
            return '#FECD07'
        case 'ice':
            return '#80DEEA'
        case 'dragon':
            return '#673BB7'
        case 'rock':
            return '#BDA537'
        case 'ground':    
            return '#DFB352'  
    }
}

const getTypeColorBG = (type) =>{
    switch(type.toLowerCase()){
        case 'water':
            return 'from-[white] to-[#84c3f5] '
        case 'fire':
            return 'from-[white] to-[#ffa66e]'
        case 'grass':
            return 'from-[white] to-[#71e376]'
        case 'flying':
            return 'from-[white] to-[#c1d7e8]'
        case 'poison':
            return 'from-[white] to-[#eb7aff]'
        case 'normal':
            return 'from-[white] to-[#e8e19e]'
        case 'fighting':
            return 'from-[white] to-[#c9878d]'
        case 'psychic':
            return 'from-[white] to-[#fc7ca7]'
        case 'ghost':
            return 'from-[white] to-[#9584ab]'
        case 'dark':
            return 'from-[white] to-[#9e9e9e]'
        case 'bug':
            return 'from-[white] to-[#d0f261]'
        case 'fairy':
            return 'from-[white] to-[#edabcc]'
        case 'steel':
            return 'from-[white] to-[#d3d3f5]'
        case 'electric':
            return 'from-[white] to-[#ffe373]'
        case 'ice':
            return 'from-[white] to-[#d2f3f7]'
        case 'dragon':
            return 'from-[white] to-[#9bb2c9]'
        case 'rock':
            return 'from-[white] to-[#bdb17b]'
        case 'ground':    
            return 'from-[white] to-[#e3c47f]'
    }
}

function destravarScroll(){
    window.onscroll=function(){};
}
</script>


<style>
.statBar{
    animation: statBarAnimation 0.7s;
}

@keyframes statBarAnimation {
    0%{
        width: 0px;
    }
}
</style>