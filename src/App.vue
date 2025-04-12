<script setup>
import { ref } from 'vue';
import MapComponent from './MapComponent.vue'; // Импортируем компонент карты

const activeTab = ref('analytics');

const setActiveTab = (tab) => {
  activeTab.value = tab;
};
</script>

<template>
  <div class="app-container">
    <header>
      <div class="nav-buttons">
        <button 
          class="nav-button" 
          :class="{ active: activeTab === 'map' }"
          @click="setActiveTab('map')"
        >
          Карта
        </button>
        <button 
          class="nav-button" 
          :class="{ active: activeTab === 'analytics' }"
          @click="setActiveTab('analytics')"
        >
          Аналитика
        </button>
      </div>
    </header>

    <main>
      <div class="dashboard-container">
        <iframe
          v-if="activeTab === 'analytics'"
          src="https://datalens.yandex/ks3o1nqdhejs6?_theme=dark&_lang=ru"
        ></iframe>
        
        <MapComponent v-if="activeTab === 'map'" />
      </div>
    </main>
  </div>
</template>

<style scoped>
.app-container {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}

header {
  background: #f0f2f5;
  padding: 1rem;
  box-shadow: 0 2px 8px rgba(0,0,0,0.1);
  flex-shrink: 0;
}

.nav-buttons {
  display: flex;
  gap: 1rem;
  max-width: 1200px;
  margin: 0 auto;
}

.nav-button {
  padding: 0.75rem 2rem;
  background: #e9ecef;
  color: #495057;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  transition: all 0.3s ease;
  font-weight: 500;
}

.nav-button:hover {
  background: #dee2e6;
}

.nav-button.active {
  background: #42b983;
  color: white;
}

main {
  flex: 1;
  display: flex;
}

.dashboard-container {
  flex: 1;
  position: relative;
  width: 100%;
}

iframe {
  width: 100%;
  height: 100%;
  border: none;
}

@media (max-width: 768px) {
  .nav-buttons {
    justify-content: center;
  }
  
  .nav-button {
    padding: 0.6rem 1.2rem;
    font-size: 0.9rem;
  }
}
</style>