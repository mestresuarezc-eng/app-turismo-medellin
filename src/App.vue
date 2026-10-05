<script setup>
import { ref, computed } from 'vue'
import MapView from './components/MapView.vue'
import RecommendationsSheet from './components/RecommendationsSheet.vue'

const mapType = ref('roadmap')
const selectedCategory = ref('all')
const selectedDestination = ref(null)

const places = ref([
  { 
    id: 1, 
    name: 'Plaza Botero y Museo de Antioquia', 
    category: 'Cultura', 
    rating: 4.8, 
    desc: 'Esculturas al aire libre del maestro Fernando Botero.', 
    image: 'https://images.unsplash.com/photo-1599581420790-24424756574c?auto=format&fit=crop&w=300&q=80', 
    coords: [6.2518, -75.5682] 
  },
  { 
    id: 2, 
    name: 'Mercado del Río', 
    category: 'Gastronomía', 
    rating: 4.9, 
    desc: 'Gran oferta gastronómica nacional e internacional.', 
    image: 'https://images.unsplash.com/photo-1555396273-367ea4eb4db5?auto=format&fit=crop&w=300&q=80', 
    coords: [6.2238, -75.5750] 
  },
  { 
    id: 3, 
    name: 'Parque Arví y Metrocable', 
    category: 'Naturaleza', 
    rating: 4.9, 
    desc: 'Reserva natural ecológica accesible en Metrocable.', 
    image: 'https://images.unsplash.com/photo-1501785888041-af3ef285b470?auto=format&fit=crop&w=300&q=80', 
    coords: [6.2811, -75.5034] 
  },
  { 
    id: 4, 
    name: 'Graffitour Comuna 13', 
    category: 'Cultura', 
    rating: 4.9, 
    desc: 'Arte urbano, escaleras eléctricas e historia de transformación.', 
    image: 'https://images.unsplash.com/photo-1582650625119-3a31f8fa2699?auto=format&fit=crop&w=300&q=80', 
    coords: [6.2514, -75.6148] 
  },
  { 
    id: 5, 
    name: 'Jardín Botánico de Medellín', 
    category: 'Naturaleza', 
    rating: 4.7, 
    desc: 'Espacio verde con el famoso Orquideorama.', 
    image: 'https://images.unsplash.com/photo-1585320806297-9794b3e4eeae?auto=format&fit=crop&w=300&q=80', 
    coords: [6.2707, -75.5647] 
  }
])

const filteredPlaces = computed(() => {
  if (selectedCategory.value === 'all') return places.value
  return places.value.filter(p => p.category === selectedCategory.value)
})
</script>

<template>
  <div class="app-container">
    <!-- Encabezado superior flotante -->
    <header class="header-overlay">
      <h1 class="text-sm font-bold text-slate-800">Enyo</h1>
      <button 
        @click="mapType = mapType === 'roadmap' ? 'satellite' : 'roadmap'"
        class="text-xs bg-slate-100 hover:bg-slate-200 text-slate-700 px-3 py-1.5 rounded-xl font-medium transition"
      >
        {{ mapType === 'roadmap' ? '🛰️ Satélite' : '🗺️ Mapa' }}
      </button>
    </header>

    <!-- Mapa -->
    <MapView 
      :destinations="filteredPlaces"
      :selectedDestination="selectedDestination"
      :mapType="mapType"
      @select-destination="d => selectedDestination = d"
    />

    <!-- Panel de Recomendaciones flotante -->
    <div class="sheet-overlay">
      <RecommendationsSheet 
        :destinations="filteredPlaces"
        :selectedDestination="selectedDestination"
        :selectedCategory="selectedCategory"
        @update:category="cat => selectedCategory = cat"
        @select-destination="d => selectedDestination = d"
      />
    </div>
  </div>
</template>

<style scoped>
.app-container {
  position: relative;
  width: 100vw;
  height: 100vh;
  overflow: hidden;
  background-color: #0f172a;
}

.header-overlay {
  position: absolute;
  top: 12px;
  left: 12px;
  right: 12px;
  z-index: 20;
  background-color: rgba(255, 255, 255, 0.95);
  backdrop-filter: blur(8px);
  border-radius: 16px;
  padding: 12px;
  box-shadow: 0 10px 15px -3px rgba(0, 0, 0, 0.1);
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.sheet-overlay {
  position: absolute;
  bottom: 0;
  left: 0;
  right: 0;
  z-index: 20;
}
</style>
