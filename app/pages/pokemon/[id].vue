<script setup>
 const route = useRoute()
 
 const {data, pending, error} = await useAsyncData( `pokemon-${route.params.id}`,
  async ()=>{
   const p = await $fetch(`https://pokeapi.co/api/v2/pokemon/${route.params.id}`)

   return {
      id: p.id,
      name: p.name.charAt(0).toUpperCase() + p.name.slice(1),
      image: p.sprites.other['official-artwork'].front_default,
      types: p.types.map(t => t.type.name),
      height: p.height / 10,
      weight: p.weight / 10,
      abilities: p.abilities.map(a => a.ability.name),
      stats: p.stats.map(s => ({
        name: s.stat.name,
        value: s.base_stat
      }))
    }
 })
</script>
<template>
   <div class="show-container">
    <NuxtLink to="/" class="back-link">← Retour au Pokédex</NuxtLink>

    <div v-if="pending">
      <el-skeleton :rows="6" animated />
    </div>

    <el-alert v-else-if="error" title="Erreur de chargement" type="error" :description="error.message" show-icon />

    <div v-else class="pokemon-details">
      <h1>#{{ data.id }} {{ data.name }}</h1>
      <img :src="data.image" :alt="data.name" class="pokemon-image" />
     
      <div class="types">
        <el-tag v-for="t in data.types" :key="t" size="medium" effect="dark" class="type-tag">
          {{ t }}
        </el-tag>
      </div>   

      <p><strong>Taille</strong>{{ data.height }}</p>
      <p><strong>Poids</strong>{{ data.weight }}</p>

    <h3>Talents</h3>  
    <ul>
      <li v-for="ability in data.abilities" :key="ability">{{ ability }}</li>
    </ul>

    <h3>Stats</h3>
    <ul>
      <li v-for="stat in data.stats" :key="stat.name">
        {{ stat.name }}: {{ stat.value }}
      </li>
    </ul>

    </div>  

   </div> 
   
</template>
<style scoped>
.show-container {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
}
.back-link {
  display: inline-block;
  margin-bottom: 20px;
  color: #409EFF;
  text-decoration: none;
}
.pokemon-image {
  width: 200px;
  height: 200px;
  display: block;
  margin: 0 auto 20px;
}
.type-tag {
  margin-right: 5px;
}
</style>