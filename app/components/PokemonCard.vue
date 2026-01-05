<script setup>

const props = defineProps({
  pokemon: {
    type: Object,
    required: true
  }
})

</script>

<template>
  <el-card class="pokemon-card">
    <img :src="props.pokemon.image" :alt="props.pokemon.name" class="pokemon-image" />
    <div class="info">
    <h3>{{ props.pokemon.name }}</h3>
    <div class="types">
        <el-tag v-for="t in pokemon.types" :key="t" size="small" effect="dark" class="type-tag">
          {{ t }}
        </el-tag>
    </div>
    <div class="stats">
        <span>{{ pokemon.height }}m</span>
        <span>{{ pokemon.weight }}kg</span>
    </div>
    </div>
  </el-card>
</template>

<style scoped>
.pokemon-image {
  width: 120px;
  height: 120px;
  display: block;
  margin: 0 auto;
}
.pokemon-card {
  text-align: center;
  transition: transform 0.2s;
}
.pokemon-card:hover {
  transform: scale(1.05);
}
.type-tag { 
  text-transform: capitalize; 
}
.stats {
  display: flex; 
  justify-content: space-around; 
  font-size: 0.8rem; 
  color: #666; 
  border-top: 1px solid #eee; 
  padding-top: 10px; 
}
</style>
