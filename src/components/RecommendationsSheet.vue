<script setup>
import { ref } from 'vue'

const props = defineProps({
  destinations: { type: Array, default: () => [] },
  selectedCategory: { type: String, default: 'all' },
  selectedDestination: { type: Object, default: null }
})

const emit = defineEmits(['update:category', 'select-destination'])

const isExpanded = ref(false)
const categories = ['all', 'Gastronomía', 'Cultura', 'Naturaleza']

function getEstimatedFare(placeName) {
  const baseFares = {
    'Plaza Botero y Museo de Antioquia': { indrive: '$9.000 - $12.000', uber: '$12.000 - $15.000' },
    'Mercado del Río': { indrive: '$11.000 - $14.000', uber: '$14.000 - $18.000' },
    'Parque Arví y Metrocable': { indrive: '$22.000 - $28.000', uber: '$28.000 - $35.000' },
    'Graffitour Comuna 13': { indrive: '$13.000 - $16.000', uber: '$16.000 - $21.000' },
    'Jardín Botánico de Medellín': { indrive: '$10.000 - $13.000', uber: '$13.000 - $17.000' }
  }
  return baseFares[placeName] || { indrive: '$10.000 - $14.000', uber: '$13.000 - $18.000' }
}
</script>

<template>
  <div 
    class="sheet-panel"
    :style="{ height: isExpanded ? '68vh' : '230px' }"
  >
    <!-- Agarre del panel -->
    <div @click="isExpanded = !isExpanded" class="sheet-handle">
      <div class="handle-bar"></div>
      <span class="handle-text">
        {{ isExpanded ? 'Toca para contraer' : 'Toca para ver sugerencias' }}
      </span>
    </div>

    <!-- Categorías -->
    <div class="categories-row">
      <button 
        v-for="cat in categories" 
        :key="cat"
        @click="emit('update:category', cat)"
        class="cat-btn"
        :class="{ active: selectedCategory === cat }"
      >
        {{ cat === 'all' ? 'Todos' : cat }}
      </button>
    </div>

    <!-- Lista de tarjetas -->
    <div class="list-body">
      <div 
        v-for="place in destinations" 
        :key="place.id"
        @click="emit('select-destination', place)"
        class="place-card"
        :class="{ selected: selectedDestination?.id === place.id }"
      >
        <img :src="place.image" :alt="place.name" class="place-img" />
        
        <div class="place-info">
          <div>
            <div class="title-row">
              <h4 class="place-title">{{ place.name }}</h4>
              <span class="place-rating">★ {{ place.rating }}</span>
            </div>
            <p class="place-desc">{{ place.desc }}</p>
          </div>
          
          <!-- Precios estimados de transporte -->
          <div class="fares-box">
            <span class="fare-tag">🚗 InDrive: <b>{{ getEstimatedFare(place.name).indrive }}</b></span>
            <span class="fare-tag">🚙 Uber: <b>{{ getEstimatedFare(place.name).uber }}</b></span>
          </div>

          <div class="place-footer">
            <span class="place-category-pill">{{ place.category }}</span>
            <span class="btn-route">Ver Ruta</span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.sheet-panel {
  width: 100%;
  background-color: #ffffff !important;
  border-top-left-radius: 24px;
  border-top-right-radius: 24px;
  box-shadow: 0 -8px 24px rgba(0, 0, 0, 0.2);
  display: flex;
  flex-direction: column;
  transition: height 0.3s ease;
  overflow: hidden;
  border-top: 1px solid #e2e8f0;
}

.sheet-handle {
  width: 100%;
  padding: 10px 0;
  display: flex;
  flex-direction: column;
  align-items: center;
  cursor: pointer;
  background-color: #f8fafc;
  border-bottom: 1px solid #f1f5f9;
  flex-shrink: 0;
}

.handle-bar {
  width: 40px;
  height: 5px;
  background-color: #cbd5e1;
  border-radius: 999px;
  margin-bottom: 4px;
}

.handle-text {
  font-size: 11px;
  color: #64748b;
  font-weight: 500;
}

.categories-row {
  padding: 10px 16px;
  display: flex;
  gap: 8px;
  overflow-x: auto;
  background-color: #ffffff;
  border-bottom: 1px solid #f1f5f9;
  flex-shrink: 0;
}

.cat-btn {
  padding: 6px 14px;
  border-radius: 999px;
  font-size: 12px;
  font-weight: 500;
  white-space: nowrap;
  border: none;
  background-color: #f1f5f9;
  color: #475569;
  cursor: pointer;
  transition: all 0.2s;
}

.cat-btn.active {
  background-color: #2563eb;
  color: #ffffff;
}

.list-body {
  flex: 1;
  overflow-y: auto;
  padding: 12px 16px;
  background-color: #ffffff;
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.place-card {
  display: flex;
  gap: 12px;
  background-color: #f8fafc;
  padding: 12px;
  border-radius: 16px;
  border: 1px solid #e2e8f0;
  cursor: pointer;
  transition: all 0.2s;
}

.place-card.selected {
  border-color: #2563eb;
  background-color: #eff6ff;
}

.place-img {
  width: 68px;
  height: 68px;
  border-radius: 12px;
  object-fit: cover;
  background-color: #cbd5e1;
  flex-shrink: 0;
}

.place-info {
  flex: 1;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  overflow: hidden;
}

.title-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.place-title {
  font-size: 13px;
  font-weight: 700;
  color: #1e293b;
  margin: 0;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
  max-width: 170px;
}

.place-rating {
  font-size: 11px;
  color: #f59e0b;
  font-weight: 700;
}

.place-desc {
  font-size: 11px;
  color: #64748b;
  margin: 2px 0 4px 0;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.fares-box {
  display: flex;
  gap: 10px;
  background: #f1f5f9;
  padding: 4px 8px;
  border-radius: 8px;
  margin: 4px 0;
}

.fare-tag {
  font-size: 10px;
  color: #334155;
}

.place-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-top: 4px;
}

.place-category-pill {
  font-size: 10px;
  background: #e2e8f0;
  color: #475569;
  padding: 2px 8px;
  border-radius: 6px;
  font-weight: 500;
}

.btn-route {
  font-size: 10px;
  background-color: #2563eb;
  color: #ffffff;
  padding: 4px 10px;
  border-radius: 8px;
  font-weight: 600;
}
</style>
