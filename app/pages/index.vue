<script setup>
import { ref, computed, onMounted } from 'vue'

const search =ref('')
const selectedType = ref('')

// --- Appel à l'API ---
  
const { data: pokemons, pending, error } = await useAsyncData('pokemons-list', async () => {
  // 1. Récupérer la liste des 151 premiers
  const response = await $fetch('https://pokeapi.co/api/v2/pokemon?limit=151')
  
  // 2. Récupérer les détails pour chaque Pokémon en parallèle
  const detailPromises = response.results.map(p => $fetch(p.url))
  const details = await Promise.all(detailPromises)
  
  // 3. Transformer les données 
  return details.map(p => ({
    id: p.id,
    name: p.name.charAt(0).toUpperCase() + p.name.slice(1),
    image: p.sprites.other['official-artwork'].front_default,
    types: p.types.map(t => t.type.name),
    height: p.height / 10,
    weight: p.weight / 10
  }))
})

  
const filteredPokemons = computed(() => {
  if (!pokemons.value) return []
  return pokemons.value.filter(p => {
    const matchesName = p.name.toLowerCase().includes(search.value.toLowerCase())
    const matchesType = !selectedType.value || p.types.includes(selectedType.value)
    return matchesName && matchesType
  })
})

</script>

<template>

<div class="container">
    <h1>Pokédex</h1>
    <input v-model="search" placesholder="Rechercher un Pokémon par nom..."/>

    <div v-if="pending" class="status-message">
      <el-skeleton :rows="5" animated />
      <p>Chargement des Pokémon...</p>
    </div>
    
    <div v-else-if="error" class="status-message error">
      <el-alert title="Erreur de connexion" type="error" description="Impossible de charger les données de l'API." show-icon />
    </div>
    
    <div class="filters">
      <span>Filtrer par type : </span>
      <el-radio-group v-model="selectedType" size="small">
        <el-radio-button label="">All</el-radio-button>
        <el-radio-button label="grass">Grass</el-radio-button>
        <el-radio-button label="fire">Fire</el-radio-button>
        <el-radio-button label="water">Water</el-radio-button>
      </el-radio-group>
    </div>

    <div class="pokemon-list">
      <PokemonCard v-for="pokemon in filteredPokemons":key="pokemon.name":pokemon="pokemon"/>
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