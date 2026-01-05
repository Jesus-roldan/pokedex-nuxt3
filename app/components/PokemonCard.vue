<script setup>
import { computed } from 'vue'

const props = defineProps({
  pokemon: {
    type: Object,
    required: true
  }
})

const pokemonTypes = computed(() => {
  if (!props.pokemon || !props.pokemon.type) {
    return ''
  }
  return Array.isArray(props.pokemon.type)
    ? props.pokemon.type.join(', ')
    : props.pokemon.type
})

</script>

<template>
  <el-card>
    <h3>{{ props.pokemon.name }}</h3>
    <p>Type: {{ pokemonTypes }}</p>
  </el-card>
</template>
