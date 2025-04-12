<template>
  <div class="map-container">
    <input 
      type="file" 
      @change="handleFileUpload"
      accept=".csv"
      class="file-input"
    />
    <div id="map"></div>
  </div>
</template>

<script setup>
import { onMounted, ref } from 'vue';
import L from 'leaflet';
import Papa from 'papaparse';
import 'leaflet/dist/leaflet.css';

const map = ref(null);

// Инициализация карты
onMounted(() => {
  map.value = L.map('map').setView([55.751244, 37.618423], 5); // Центр на Москве
  
  L.tileLayer('https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png', {
    maxZoom: 19,
    attribution: '© OpenStreetMap'
  }).addTo(map.value);
});

// Обработка загрузки файла
const handleFileUpload = (event) => {
  const file = event.target.files[0];
  if (file) {
    Papa.parse(file, {
      header: true,
      skipEmptyLines: true,
      complete: (results) => {
        results.data.forEach((row) => {
          const lat = parseFloat(row.latitude);
          const lng = parseFloat(row.longitude);
          if (!isNaN(lat) && !isNaN(lng)) {
            L.marker([lat, lng])
              .addTo(map.value)
              .bindPopup(`<b>Координаты:</b><br>Широта: ${lat}<br>Долгота: ${lng}`);
          }
        });
      }
    });
  }
};
</script>

<style scoped>
.map-container {
  height: 100%;
  position: relative;
}

#map {
  height: calc(100vh - 70px); /* Учитываем высоту шапки и инпута */
  width: 100%;
}

.file-input {
  position: absolute;
  top: 10px;
  left: 10px;
  z-index: 1000;
  padding: 8px;
  background: white;
  border-radius: 4px;
  box-shadow: 0 2px 4px rgba(0,0,0,0.2);
}
</style>