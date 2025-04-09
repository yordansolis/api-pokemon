<template>
  <div>
    <h1>API de Pokémon</h1>

    <ul>
      <li v-for="post in paginatedPosts" :key="post.id">
       {{ post.id }}. {{ post.title }}
        <img :src="post.image" alt="Imagen de {{ post.title }}" />
      </li>
    </ul>

    <div>
      <button @click="prevPage" :disabled="page === 1">Anterior</button>
      <span>Página {{ page }} de {{ totalPages }}</span>
      <button @click="nextPage" :disabled="page === totalPages">Siguiente</button>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'
import axios from 'axios'

// Variables reactivas
const posts = ref([])        // Lista completa de posts
const page = ref(1)          // Página actual
const perPage = 10           // Cantidad de elementos por página

// Cargar los datos
const fetchPosts = async () => {
  try {
    const res = await axios.get('https://pokeapi.co/api/v2/pokemon?limit=100') // lo limitamos a 100
    // console.log(res.data);

    // Selecionamos solo los primeros 10 elementos con su id, name, image
    posts.value = res.data.results.map((item, index) => ({
      id: index + 1,
      title: item.name,

      image: `https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${index + 1}.png`,
    }))
    // console.log(posts.value);
    

    
  } catch (error) {
    console.error('Error al obtener los posts:', error)
  }
}

// Calcular total de páginas - para iniciar en la página 1, obtenemos una value de 0
const totalPages = computed(() => {
  return Math.ceil(posts.value.length / perPage)
})

// Obtener los posts de la página actual
const paginatedPosts = computed(() => {
  const start = (page.value - 1) * perPage
  return posts.value.slice(start, start + perPage)
})

// Cambiar de página
const nextPage = () => {
  if (page.value < totalPages.value) page.value++
}

const prevPage = () => {
  if (page.value > 1) page.value--
}

// Ejecutar al cargar el componente
onMounted(fetchPosts)
</script>
