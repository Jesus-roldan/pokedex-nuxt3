<script setup>
import { ref, computed, onMounted } from 'vue'

const search = ref('')
const selectedType = ref('')
const showOnlyFavorites = ref(false)
const favorites = ref([])


onMounted(() => {
  const saved = localStorage.getItem('pokedex-favs')
  if (saved) {
    favorites.value = JSON.parse(saved)
  }
})

const toggleFavorite = (id) => {
  if (favorites.value.includes(id)) {
    favorites.value = favorites.value.filter(favId => favId !== id)
  } else {
    favorites.value.push(id)
  }
  localStorage.setItem('pokedex-favs', JSON.stringify(favorites.value))
}

const mapPokemon = (p) => ({
  id: p.id,
  name: p.name.charAt(0).toUpperCase() + p.name.slice(1),
  image: p.sprites.other['official-artwork'].front_default,
  types: p.types.map(t => t.type.name),
  height: p.height / 10,
  weight: p.weight / 10
})

const currentPage = ref(1)
const limit = 20
const totalByType = ref(0)

const {data, pending, error, refresh} = await useAsyncData('pokemon', async ()=>{

  if (search.value){
    const p = await $fetch(`https://pokeapi.co/api/v2/pokemon/${search.value.toLowerCase()}`)

    totalByType.value = 1
    return [mapPokemon(p)]
  } 

  if (selectedType.value) {       
    const typeData = await $fetch(`https://pokeapi.co/api/v2/type/${selectedType.value}?limit=${limit}&offset=${(currentPage.value -1) * limit}`) 
    
    totalByType.value = typeData.pokemon.length

    const start = (currentPage.value -1) * limit
    const end = start + limit

    const pagePokemon = typeData.pokemon.slice(start, end)
    
    const details = await Promise.all(
        pagePokemon.map(p =>
          $fetch(p.pokemon.url)
        )
    )
    return details.map(mapPokemon)    
  }
  totalByType.value = 0
  return []
  }, 
  {
    watch: [search, selectedType, currentPage]
  }

)
refresh()

const filteredPokemons = computed(() => {
  if (!data.value) return []
  return data.value.filter(p => {    
    const matchesFav = !showOnlyFavorites.value || favorites.value.includes(p.id)

    return matchesFav
  })
})

</script>

<template>

<div class="container">
    <h1>Pokédex</h1>
    <input v-model="search" placeholder="Rechercher un Pokémon"/>
    

    <div v-if="pending" class="status-message">
      <el-skeleton :rows="4" animated />
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
    
    <el-switch
          v-model="showOnlyFavorites"
          active-text="Voir uniquement mes favoris"
          style="margin-left: 20px"
    />

    <div class="pokemon-list">
      <PokemonCard v-for="pokemon in filteredPokemons"
      :key="pokemon.id"
      :pokemon="pokemon"
      :is-favorite="favorites.includes(pokemon.id)"
      @toggle-fav="toggleFavorite(pokemon.id)" 
      />
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