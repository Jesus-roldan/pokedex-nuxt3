<script setup>
import { ref, computed } from 'vue'

const search =ref('')

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
  return pokemons.value.filter(pokemon =>
    pokemon.name.toLowerCase().includes(search.value.toLowerCase())
  )
})


</script>

<template>

<div class="container">
    <h1>Pokédex</h1>
    <input v-model="search" placesholder="Rechercher un Pokémon par nom..."/>

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

.pokemon-list {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(150px, 1fr));
  gap: 1rem;
  margin-top: 2rem;
}
</style>