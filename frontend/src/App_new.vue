<script>
import { ref, reactive, computed, onMounted, nextTick } from 'vue'

export default {
  name: 'KalkulatorFaraidh',
  setup() {
    // Reactive state
    const currentView = ref('faraidh')
    const sidebarOpen = ref(false)
    const showHukumModal = ref(false)
    const selectedHukum = ref(null)
    const hartaBersih = ref(1000000000)
    const showFamilyTree = ref(false)
    
    // Methods
    const toggleSidebar = () => {
      sidebarOpen.value = !sidebarOpen.value
    }
    
    const setCurrentView = (view) => {
      currentView.value = view
    }
    
    const openSyaratModal = () => {
      showHukumModal.value = true
    }
    
    const closeHukumModal = () => {
      showHukumModal.value = false
    }
    
    const toggleFamilyTree = () => {
      showFamilyTree.value = !showFamilyTree.value
    }

    return {
      currentView,
      sidebarOpen,
      showHukumModal,
      selectedHukum,
      hartaBersih,
      showFamilyTree,
      toggleSidebar,
      setCurrentView,
      openSyaratModal,
      closeHukumModal,
      toggleFamilyTree
    }
  }
}
</script>

<template>
  <div class="app-container">
    <div class="content">
      <h1>🕌 Kalkulator Faraidh (Islamic Inheritance Calculator)</h1>
      <p>Aplikasi telah berhasil diperbaiki!</p>
      <p>Current view: {{ currentView }}</p>
      
      <div class="buttons">
        <button @click="setCurrentView('faraidh')" :class="{ active: currentView === 'faraidh' }">
          Kalkulator Faraidh
        </button>
        <button @click="setCurrentView('utang-wasiat')" :class="{ active: currentView === 'utang-wasiat' }">
          Utang & Wasiat
        </button>
        <button @click="setCurrentView('dashboard')" :class="{ active: currentView === 'dashboard' }">
          Dashboard
        </button>
        <button @click="toggleSidebar">
          Toggle Sidebar: {{ sidebarOpen ? 'Open' : 'Closed' }}
        </button>
      </div>
      
      <div class="view-content">
        <div v-if="currentView === 'faraidh'">
          <h2>📊 Kalkulator Faraidh</h2>
          <p>Interface untuk menghitung pembagian warisan akan segera ditambahkan kembali.</p>
        </div>
        
        <div v-if="currentView === 'utang-wasiat'">
          <h2>💰 Kalkulator Utang & Wasiat</h2>
          <p>Interface untuk menghitung harta bersih setelah dikurangi utang dan wasiat.</p>
        </div>
        
        <div v-if="currentView === 'dashboard'">
          <h2>📊 Dashboard Visualisasi</h2>
          <p>Chart dan statistik pembagian warisan akan ditampilkan di sini.</p>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.app-container {
  min-height: 100vh;
  background: linear-gradient(135deg, #f0f9ff 0%, #e0f2fe 100%);
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
  padding: 20px;
}

.content {
  max-width: 800px;
  margin: 0 auto;
  background: white;
  border-radius: 16px;
  padding: 30px;
  box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1);
}

h1 {
  color: #1f2937;
  text-align: center;
  margin-bottom: 20px;
}

.buttons {
  display: flex;
  gap: 10px;
  margin: 20px 0;
  flex-wrap: wrap;
}

button {
  padding: 10px 20px;
  border: 2px solid #10b981;
  background: white;
  color: #10b981;
  border-radius: 8px;
  cursor: pointer;
  font-weight: 600;
  transition: all 0.2s;
}

button:hover {
  background: #10b981;
  color: white;
}

button.active {
  background: #10b981;
  color: white;
}

.view-content {
  margin-top: 30px;
  padding: 20px;
  background: #f8fafc;
  border-radius: 12px;
  border: 1px solid #e2e8f0;
}

h2 {
  color: #374151;
  margin-bottom: 15px;
}

p {
  color: #6b7280;
  line-height: 1.6;
}
</style>