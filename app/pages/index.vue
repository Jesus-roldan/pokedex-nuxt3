<script setup>
import { ref, computed } from 'vue'

const search =ref('')
const selectedType = ref('')

const pokemons = ref([
  { name: 'Bulbasaur', type: 'Grass/Poison' },
  { name: 'Ivysaur', type: 'Grass/Poison' },
  { name: 'Venusaur', type: 'Grass/Poison' },
  { name: 'Charmander', type: 'Fire' },
  { name: 'Charmeleon', type: 'Fire' },
  { name: 'Charizard', type: 'Fire/Flying' },
  { name: 'Squirtle', type: 'Water' },
  { name: 'Wartortle', type: 'Water' },
  { name: 'Blastoise', type: 'Water' }
])

const filteredPokemons = computed(() => {
  const query = search.value.toLowerCase()

  return pokemons.value.filter(pokemon => {
    const name = pokemon.name ? pokemon.name.toLowerCase() : ''
    const matchesName = name.includes(query)

    const type = pokemon.type || '' 
    const matchesType = !selectedType.value || type.includes(selectedType.value)
    
    return matchesName && matchesType
  })
})


</script>

<template>

<div class="container">
    <h1>Pokédex</h1>
    <input v-model="search" placesholder="Rechercher un Pokémon par nom..."/>

    <div class="pokemon-list">
      <PokemonCard v-for="pokemon in filteredPokemons":key="pokemon.name":pokemon="pokemon"/>
    </div>

    <div class="filters">
      <span>Filtrer par type : </span>
      <el-radio-group v-model="selectedType" size="small">
        <el-radio-button label="">Todos</el-radio-button>
        <el-radio-button label="Grass">Grass</el-radio-button>
        <el-radio-button label="Fire">Fire</el-radio-button>
        <el-radio-button label="Water">Water</el-radio-button>
      </el-radio-group>
    </div>


</div>

</template>

<style scoped>
.container {
  max-width: 800px;
  margin: 0 auto;
  padding: 2rem;
}

.filters {
  margin-bottom: 2rem;
}

.filters span {
  margin-right: 10px;
  font-weight: bold;
}

.pokemon-list {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(150px, 1fr));
  gap: 1rem;
  margin-top: 2rem;
}
</style>