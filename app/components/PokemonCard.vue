<script setup>
import { Star } from '@element-plus/icons-vue'

const props = defineProps({
  pokemon: {
    type: Object,
    required: true
  },
  isFavorite: {
    type: Boolean,
    required: true
  }
})

const emit = defineEmits(['toggle-fav'])

</script>

<template>
  <nuxt-link :to="`/pokemon/${pokemon.id}`">
    <el-card class="pokemon-card">
      <div class="card-header">
        <span class="id">#{{ pokemon.id }}</span>
        <el-button class="fav-btn"
          :type="isFavorite ? 'danger' : 'info'"
          :icon="Star"
          circle
          size="10" 
          @click="emit('toggle-fav') "
        />
      </div>
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
  </nuxt-link> 
</template>

<style scoped>
 .card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.id {
  font-weight: bold;
}
.fav-btn {
  font-size: 2rem;
} 
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
