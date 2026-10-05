<script setup>
import { ref, onMounted, onUnmounted, watch, nextTick } from 'vue'
import L from 'leaflet'
import 'leaflet/dist/leaflet.css'
import 'leaflet-routing-machine'
import 'leaflet-routing-machine/dist/leaflet-routing-machine.css'

const props = defineProps({
  destinations: { type: Array, default: () => [] },
  selectedDestination: { type: Object, default: null },
  mapType: { type: String, default: 'roadmap' }
})

const emit = defineEmits(['select-destination'])

const mapContainer = ref(null)
let map = null
let tileLayer = null
let routingControl = null
let markers = []

const userLocation = [6.2442, -75.5812] // Centro de Medellín

const tileUrls = {
  roadmap: 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png',
  satellite: 'https://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}'
}

onMounted(() => {
  nextTick(() => {
    if (!mapContainer.value) return

    map = L.map(mapContainer.value, { 
      zoomControl: false,
      attributionControl: false 
    }).setView(userLocation, 13)

    tileLayer = L.tileLayer(tileUrls[props.mapType] || tileUrls.roadmap, {
      maxZoom: 18,
      subdomains: ['a', 'b', 'c']
    }).addTo(map)

    // Marcador de ubicación actual
    L.circleMarker(userLocation, {
      radius: 8,
      fillColor: '#2563eb',
      color: '#ffffff',
      weight: 2,
      fillOpacity: 1
    }).addTo(map)

    renderMarkers()

    setTimeout(() => { if (map) map.invalidateSize() }, 300)
  })
})

onUnmounted(() => {
  if (map) {
    map.remove()
    map = null
  }
})

watch(() => props.mapType, (newType) => {
  if (tileLayer && map) {
    map.removeLayer(tileLayer)
    tileLayer = L.tileLayer(tileUrls[newType] || tileUrls.roadmap, { maxZoom: 18 }).addTo(map)
  }
})

watch(() => props.destinations, () => {
  if (map) renderMarkers()
}, { deep: true })

// Dibuja la ruta real por las calles al seleccionar un destino
watch(() => props.selectedDestination, (dest) => {
  if (!map) return

  // Remover la ruta anterior si existe
  if (routingControl) {
    map.removeControl(routingControl)
    routingControl = null
  }

  if (dest && dest.coords) {
    routingControl = L.Routing.control({
      waypoints: [
        L.latLng(userLocation[0], userLocation[1]),
        L.latLng(dest.coords[0], dest.coords[1])
      ],
      routeWhileDragging: false,
      addWaypoints: false,
      draggableWaypoints: false,
      fitSelectedRoutes: true,
      showAlternatives: false,
      lineOptions: {
        styles: [{ color: '#2563eb', weight: 6, opacity: 0.85 }]
      },
      // Oculta la caja de texto flotante predeterminada para que no estorbe tu diseño
      createMarker: () => null
    }).addTo(map)
  }
})

function renderMarkers() {
  if (!map) return
  markers.forEach(m => map.removeLayer(m))
  markers = []

  if (Array.isArray(props.destinations)) {
    markers = props.destinations.map(d => {
      return L.marker(d.coords)
        .addTo(map)
        .on('click', () => emit('select-destination', d))
    })
  }
}
</script>

<template>
  <div ref="mapContainer" class="map-container"></div>
</template>

<style scoped>
.map-container {
  width: 100vw;
  height: 100vh;
  position: absolute;
  top: 0;
  left: 0;
  z-index: 1;
  background-color: #cbd5e1;
}
</style>
