<!-- YandexMapComponent.vue -->
<template>
  <div class="map-container">
    <div ref="mapContainer" class="map"></div>
  </div>
</template>

<script>
import { onMounted, ref } from 'vue';
import Papa from 'papaparse';

const YMAPS_API_KEY = '5731c8f1-9a43-4517-ae39-8a50fc05f37d'; // Замените на ваш ключ
const YMAPS_URL = `https://api-maps.yandex.ru/2.1/?apikey=${YMAPS_API_KEY}&lang=ru_RU`;

export default {
  name: 'YandexMapComponent',
  setup() {
    const mapContainer = ref(null);

    const loadYmaps = () => {
      return new Promise((resolve, reject) => {
        if (window.ymaps) {
          window.ymaps.ready(resolve);
          return;
        }
        
        const script = document.createElement('script');
        script.src = YMAPS_URL;
        script.onload = () => window.ymaps.ready(resolve);
        script.onerror = reject;
        document.head.appendChild(script);
      });
    };

    const initMap = async () => {
      const map = new window.ymaps.Map(mapContainer.value, {
        center: [55.751574, 37.573856],
        zoom: 5,
      });

      await loadCoordinates(map);
    };

    const loadCoordinates = (map) => {
      return fetch('/coordinates.csv')
        .then(response => response.text())
        .then(data => {
          Papa.parse(data, {
            header: true,
            complete: (results) => {
              results.data.forEach((row) => {
                const lat = parseFloat(row.latitude);
                const lng = parseFloat(row.longitude);
                
                if (!isNaN(lat) && !isNaN(lng)) {
                  new window.ymaps.Placemark(
                    [lat, lng],
                    { balloonContent: `Широта: ${lat}, Долгота: ${lng}` }
                  ).addTo(map.geoObjects);
                }
              });
            },
          });
        });
    };

    onMounted(async () => {
      try {
        await loadYmaps();
        await initMap();
      } catch (error) {
        console.error('Ошибка инициализации карты:', error);
      }
    });

    return {
      mapContainer,
    };
  },
};
</script>