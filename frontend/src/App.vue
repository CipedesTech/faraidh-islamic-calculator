<script setup>
import { ref, computed } from 'vue'
import { Chart as ChartJS, ArcElement, Tooltip, Legend } from 'chart.js'
import { Pie } from 'vue-chartjs'

ChartJS.register(ArcElement, Tooltip, Legend)

// Navigation State
const currentView = ref('faraidh') // 'faraidh', 'utang-wasiat', 'dashboard'
const sidebarOpen = ref(true)

// State untuk aplikasi
const hartaBersih = ref(1000000000)
const showSyaratModal = ref(false)
const showFamilyTree = ref(false)
const showHukumModal = ref(false)
const selectedHukum = ref(null)

// State untuk Utang & Wasiat
const utangWasiat = ref({
  totalUtang: 0,
  biayaPemakaman: 0,
  wasiat: 0,
  wasiatValid: true
})

const familyTreeData = ref({
  nodes: [],
  connections: []
})

const ahliWaris = ref({
  // Laki-laki
  anakLakiLaki: { selected: false, jumlah: 1, terhalang: false },
  ayah: { selected: false, jumlah: 1, terhalang: false },
  suami: { selected: false, jumlah: 1, terhalang: false },
  cucuLakiLaki: { selected: false, jumlah: 1, terhalang: false },
  kakek: { selected: false, jumlah: 1, terhalang: false },
  saudaraLakiLakiKandung: { selected: false, jumlah: 1, terhalang: false },
  saudaraLakiLakiSeayah: { selected: false, jumlah: 1, terhalang: false },
  saudaraLakiLakiSeibu: { selected: false, jumlah: 1, terhalang: false },
  keponakanLakiLakiKandung: { selected: false, jumlah: 1, terhalang: false },
  keponakanLakiLakiSeayah: { selected: false, jumlah: 1, terhalang: false },
  sepupuLakiLakiKandung: { selected: false, jumlah: 1, terhalang: false },
  sepupuLakiLakiSeayah: { selected: false, jumlah: 1, terhalang: false },
  
  // Perempuan
  istri: { selected: false, jumlah: 1, terhalang: false },
  anakPerempuan: { selected: false, jumlah: 1, terhalang: false },
  cucuPerempuan: { selected: false, jumlah: 1, terhalang: false },
  ibu: { selected: false, jumlah: 1, terhalang: false },
  nenekKandung: { selected: false, jumlah: 1, terhalang: false },
  nenekSeayah: { selected: false, jumlah: 1, terhalang: false },
  saudaraPerempuanKandung: { selected: false, jumlah: 1, terhalang: false },
  saudaraPerempuanSeayah: { selected: false, jumlah: 1, terhalang: false },
  saudaraPerempuanSeibu: { selected: false, jumlah: 1, terhalang: false },
  pamanKandung: { selected: false, jumlah: 1, terhalang: false },
  pamanSeayah: { selected: false, jumlah: 1, terhalang: false }
})

// Daftar ahli waris dengan informasi lengkap
const daftarAhliWaris = {
  anakLakiLaki: { label: 'Anak Laki-laki', arab: 'ابن', canHaveMultiple: true },
  ayah: { label: 'Ayah', arab: 'أب', canHaveMultiple: false },
  suami: { label: 'Suami', arab: 'زوج', canHaveMultiple: false },
  istri: { label: 'Istri', arab: 'زوجة', canHaveMultiple: true },
  cucuLakiLaki: { label: 'Cucu Laki-laki', arab: 'ابن ابن', canHaveMultiple: true },
  kakek: { label: 'Kakek', arab: 'جد', canHaveMultiple: false },
  saudaraLakiLakiKandung: { label: 'Saudara Laki-laki Kandung', arab: 'أخ شقيق', canHaveMultiple: true },
  saudaraLakiLakiSeayah: { label: 'Saudara Laki-laki Seayah', arab: 'أخ لأب', canHaveMultiple: true },
  saudaraLakiLakiSeibu: { label: 'Saudara Laki-laki Seibu', arab: 'أخ لأم', canHaveMultiple: true },
  keponakanLakiLakiKandung: { label: 'Keponakan Laki-laki (dari Sdr Lk Kandung)', arab: 'ابن أخ شقيق', canHaveMultiple: true },
  keponakanLakiLakiSeayah: { label: 'Keponakan Laki-laki (dari Sdr Lk Seayah)', arab: 'ابن أخ لأب', canHaveMultiple: true },
  sepupuLakiLakiKandung: { label: 'Sepupu Laki-laki (dari Paman Kandung)', arab: 'ابن عم شقيق', canHaveMultiple: true },
  sepupuLakiLakiSeayah: { label: 'Sepupu Laki-laki (dari Paman Seayah)', arab: 'ابن عم لأب', canHaveMultiple: true },
  anakPerempuan: { label: 'Anak Perempuan', arab: 'بنت', canHaveMultiple: true },
  cucuPerempuan: { label: 'Cucu Perempuan', arab: 'بنت ابن', canHaveMultiple: true },
  ibu: { label: 'Ibu', arab: 'أم', canHaveMultiple: false },
  nenekKandung: { label: 'Nenek Kandung', arab: 'أم أم', canHaveMultiple: false },
  nenekSeayah: { label: 'Nenek Seayah', arab: 'أم أب', canHaveMultiple: false },
  saudaraPerempuanKandung: { label: 'Saudara Perempuan Kandung', arab: 'أخت شقيقة', canHaveMultiple: true },
  saudaraPerempuanSeayah: { label: 'Saudara Perempuan Seayah', arab: 'أخت لأب', canHaveMultiple: true },
  saudaraPerempuanSeibu: { label: 'Saudara Perempuan Seibu', arab: 'أخت لأم', canHaveMultiple: true },
  pamanKandung: { label: 'Paman Kandung', arab: 'عم شقيق', canHaveMultiple: true },
  pamanSeayah: { label: 'Paman Seayah', arab: 'عم لأب', canHaveMultiple: true }
}

// Cek apakah ahli waris terhalang
const isAhliWarisTermasuk = (key) => {
  const ahli = ahliWaris.value[key]
  return ahli.selected && !ahli.terhalang
}

// Perhitungan harta bersih setelah utang dan wasiat
const hartaBersihFinal = computed(() => {
  const totalUtang = utangWasiat.value.totalUtang
  const biayaPemakaman = utangWasiat.value.biayaPemakaman
  let wasiat = utangWasiat.value.wasiat
  
  // Validasi wasiat maksimal 1/3 harta
  const maxWasiat = hartaBersih.value / 3
  if (wasiat > maxWasiat) {
    wasiat = maxWasiat
    utangWasiat.value.wasiatValid = false
  } else {
    utangWasiat.value.wasiatValid = true
  }
  
  const totalPengurang = totalUtang + biayaPemakaman + wasiat
  return Math.max(0, hartaBersih.value - totalPengurang)
})

// Data untuk pie chart
const chartData = computed(() => {
  const hasil = hasilPembagian.value
  const labels = []
  const data = []
  const backgroundColor = [
    '#10b981', '#3b82f6', '#f59e0b', '#ef4444', '#8b5cf6', 
    '#06b6d4', '#84cc16', '#f97316', '#ec4899', '#6b7280'
  ]
  
  Object.entries(hasil).forEach(([key, value], index) => {
    const info = daftarAhliWaris[key]
    if (info) {
      labels.push(info.label)
      const jumlah = ahliWaris.value[key]?.jumlah || 1
      data.push(value * jumlah)
    }
  })
  
  return {
    labels,
    datasets: [{
      data,
      backgroundColor: backgroundColor.slice(0, data.length),
      borderWidth: 2,
      borderColor: '#ffffff'
    }]
  }
})

const chartOptions = {
  responsive: true,
  maintainAspectRatio: false,
  plugins: {
    legend: {
      position: 'bottom',
      labels: {
        usePointStyle: true,
        padding: 20,
        font: {
          size: 12
        }
      }
    },
    tooltip: {
      callbacks: {
        label: function(context) {
          const value = context.parsed
          const formatted = new Intl.NumberFormat('id-ID', {
            style: 'currency',
            currency: 'IDR',
            minimumFractionDigits: 0
          }).format(value)
          return `${context.label}: ${formatted}`
        }
      }
    }
  }
}

// Penjelasan Hukum Database
const hukumDatabase = {
  bagianSuami: {
    title: 'Bagian Suami',
    ayat: 'وَلَكُمْ نِصْفُ مَا تَرَكَ أَزْوَاجُكُمْ إِن لَّمْ يَكُن لَّهُنَّ وَلَدٌ',
    terjemah: 'Dan bagimu (suami-suami) seperdua dari harta yang ditinggalkan oleh istri-istrimu, jika mereka tidak mempunyai anak',
    surat: 'QS. An-Nisa: 12',
    penjelasan: 'Suami mendapat 1/2 jika istri tidak meninggalkan anak, dan 1/4 jika ada anak.',
    contohKasus: [
      'Istri meninggal tanpa anak → Suami dapat 1/2',
      'Istri meninggal dengan anak → Suami dapat 1/4'
    ]
  },
  bagianIstri: {
    title: 'Bagian Istri',
    ayat: 'وَلَهُنَّ الرُّبُعُ مِمَّا تَرَكْتُمْ إِن لَّمْ يَكُن لَّكُمْ وَلَدٌ',
    terjemah: 'Para istri memperoleh seperempat harta yang kamu tinggalkan jika kamu tidak mempunyai anak',
    surat: 'QS. An-Nisa: 12',
    penjelasan: 'Istri mendapat 1/4 jika suami tidak meninggalkan anak, dan 1/8 jika ada anak.',
    contohKasus: [
      'Suami meninggal tanpa anak → Istri dapat 1/4',
      'Suami meninggal dengan anak → Istri dapat 1/8'
    ]
  },
  bagianAnak: {
    title: 'Bagian Anak',
    ayat: 'يُوصِيكُمُ اللَّهُ فِي أَوْلَادِكُمْ لِلذَّكَرِ مِثْلُ حَظِّ الْأُنثَيَيْنِ',
    terjemah: 'Allah mensyariatkan bagimu tentang (pembagian pusaka untuk) anak-anakmu. Yaitu: bahagian seorang anak lelaki sama dengan bahagian dua orang anak perempuan',
    surat: 'QS. An-Nisa: 11',
    penjelasan: 'Anak laki-laki mendapat 2x lipat dari anak perempuan. Anak perempuan tunggal mendapat 1/2, dua atau lebih mendapat 2/3.',
    contohKasus: [
      '1 anak laki-laki + 1 anak perempuan → 2:1',
      '2 anak perempuan saja → 2/3 total',
      '1 anak perempuan saja → 1/2'
    ]
  },
  bagianAyah: {
    title: 'Bagian Ayah',
    ayat: 'وَلِأَبَوَيْهِ لِكُلِّ وَاحِدٍ مِّنْهُمَا السُّدُسُ مِمَّا تَرَكَ إِن كَانَ لَهُ وَلَدٌ',
    terjemah: 'Dan untuk dua orang ibu-bapa, bagi masing-masingnya seperenam dari harta yang ditinggalkan, jika yang meninggal itu mempunyai anak',
    surat: 'QS. An-Nisa: 11',
    penjelasan: 'Ayah mendapat 1/6 jika ada anak, dan dapat bertambah sebagai asabah jika tidak ada anak laki-laki.',
    contohKasus: [
      'Ada anak laki-laki → Ayah dapat 1/6 saja',
      'Tidak ada anak laki-laki → Ayah dapat 1/6 + sisa sebagai asabah'
    ]
  }
}

// Logika untuk menentukan ahli waris yang terhalang
const updateTerhalangStatus = () => {
  // Reset semua status terhalang
  Object.keys(ahliWaris.value).forEach(key => {
    ahliWaris.value[key].terhalang = false
  })

  // Logika terhalang sesuai hukum faraidh
  if (isAhliWarisTermasuk('anakLakiLaki') || isAhliWarisTermasuk('anakPerempuan')) {
    // Jika ada anak, maka cucu terhalang
    ahliWaris.value.cucuLakiLaki.terhalang = true
    ahliWaris.value.cucuPerempuan.terhalang = true
    
    // Saudara kandung dan seayah terhalang
    ahliWaris.value.saudaraLakiLakiKandung.terhalang = true
    ahliWaris.value.saudaraLakiLakiSeayah.terhalang = true
    ahliWaris.value.saudaraPerempuanKandung.terhalang = true
    ahliWaris.value.saudaraPerempuanSeayah.terhalang = true
    
    // Keponakan terhalang
    ahliWaris.value.keponakanLakiLakiKandung.terhalang = true
    ahliWaris.value.keponakanLakiLakiSeayah.terhalang = true
  }
  
  if (isAhliWarisTermasuk('ayah')) {
    // Jika ada ayah, maka kakek terhalang
    ahliWaris.value.kakek.terhalang = true
  }
  
  if (isAhliWarisTermasuk('ibu')) {
    // Jika ada ibu, maka nenek terhalang
    ahliWaris.value.nenekKandung.terhalang = true
    ahliWaris.value.nenekSeayah.terhalang = true
  }
}

// Format rupiah
const formatRupiah = (number) => {
  return new Intl.NumberFormat('id-ID', {
    style: 'currency',
    currency: 'IDR',
    minimumFractionDigits: 0
  }).format(number)
}

// Perhitungan pembagian warisan
const hasilPembagian = computed(() => {
  updateTerhalangStatus()
  
  const total = hartaBersihFinal.value // Menggunakan harta bersih setelah utang/wasiat
  const hasil = {}
  
  // Ini adalah implementasi yang disederhanakan
  let sisaHarta = total
  
  // Bagian suami/istri
  if (isAhliWarisTermasuk('suami')) {
    const adaAnak = isAhliWarisTermasuk('anakLakiLaki') || isAhliWarisTermasuk('anakPerempuan') || 
                   isAhliWarisTermasuk('cucuLakiLaki') || isAhliWarisTermasuk('cucuPerempuan')
    const bagianSuami = adaAnak ? total * 0.25 : total * 0.5
    hasil.suami = bagianSuami
    sisaHarta -= bagianSuami
  }
  
  if (isAhliWarisTermasuk('istri')) {
    const adaAnak = isAhliWarisTermasuk('anakLakiLaki') || isAhliWarisTermasuk('anakPerempuan') || 
                   isAhliWarisTermasuk('cucuLakiLaki') || isAhliWarisTermasuk('cucuPerempuan')
    const bagianIstri = adaAnak ? total * 0.125 : total * 0.25
    const jumlahIstri = ahliWaris.value.istri.jumlah
    hasil.istri = bagianIstri / jumlahIstri
    sisaHarta -= bagianIstri
  }
  
  // Bagian ayah
  if (isAhliWarisTermasuk('ayah')) {
    const adaAnak = isAhliWarisTermasuk('anakLakiLaki') || isAhliWarisTermasuk('anakPerempuan')
    const bagianAyah = adaAnak ? total * (1/6) : sisaHarta * 0.3
    hasil.ayah = bagianAyah
    sisaHarta -= bagianAyah
  }
  
  // Bagian ibu
  if (isAhliWarisTermasuk('ibu')) {
    const adaAnak = isAhliWarisTermasuk('anakLakiLaki') || isAhliWarisTermasuk('anakPerempuan') ||
                   isAhliWarisTermasuk('cucuLakiLaki') || isAhliWarisTermasuk('cucuPerempuan')
    const adaSaudara = isAhliWarisTermasuk('saudaraLakiLakiKandung') || isAhliWarisTermasuk('saudaraPerempuanKandung') ||
                      isAhliWarisTermasuk('saudaraLakiLakiSeayah') || isAhliWarisTermasuk('saudaraPerempuanSeayah')
    
    const bagianIbu = (adaAnak || adaSaudara) ? total * (1/6) : total * (1/3)
    hasil.ibu = bagianIbu
    sisaHarta -= bagianIbu
  }
  
  // Anak perempuan
  if (isAhliWarisTermasuk('anakPerempuan') && !isAhliWarisTermasuk('anakLakiLaki')) {
    const jumlahAnakPerempuan = ahliWaris.value.anakPerempuan.jumlah
    const bagianAnakPerempuan = jumlahAnakPerempuan === 1 ? total * 0.5 : total * (2/3)
    hasil.anakPerempuan = bagianAnakPerempuan / jumlahAnakPerempuan
    sisaHarta -= bagianAnakPerempuan
  }
  
  // Anak laki-laki mendapat sisa sebagai asabah
  if (isAhliWarisTermasuk('anakLakiLaki')) {
    const jumlahAnakLaki = ahliWaris.value.anakLakiLaki.jumlah
    let bagianAnakLaki = sisaHarta
    
    // Jika ada anak perempuan juga, bagi dengan rasio 2:1
    if (isAhliWarisTermasuk('anakPerempuan')) {
      const jumlahAnakPerempuan = ahliWaris.value.anakPerempuan.jumlah
      const totalBagian = (jumlahAnakLaki * 2) + jumlahAnakPerempuan
      
      hasil.anakLakiLaki = (sisaHarta * 2) / totalBagian
      hasil.anakPerempuan = sisaHarta / totalBagian
    } else {
      hasil.anakLakiLaki = bagianAnakLaki / jumlahAnakLaki
    }
  }
  
  // Jika masih ada sisa dan tidak ada anak laki-laki, distribusikan ke asabah lainnya
  if (!isAhliWarisTermasuk('anakLakiLaki') && sisaHarta > 0) {
    const ahliWarisAsabah = []
    
    if (isAhliWarisTermasuk('cucuLakiLaki')) ahliWarisAsabah.push('cucuLakiLaki')
    if (isAhliWarisTermasuk('kakek')) ahliWarisAsabah.push('kakek')
    if (isAhliWarisTermasuk('saudaraLakiLakiKandung')) ahliWarisAsabah.push('saudaraLakiLakiKandung')
    if (isAhliWarisTermasuk('saudaraLakiLakiSeayah')) ahliWarisAsabah.push('saudaraLakiLakiSeayah')
    
    if (ahliWarisAsabah.length > 0) {
      // Prioritas: cucu > kakek > saudara kandung > saudara seayah
      const prioritas = ahliWarisAsabah[0]
      hasil[prioritas] = sisaHarta / ahliWaris.value[prioritas].jumlah
    }
  }
  
  return hasil
})

const handleHartaChange = (event) => {
  const value = event.target.value.replace(/\D/g, '')
  hartaBersih.value = parseInt(value) || 0
}

const toggleAhliWaris = (key) => {
  ahliWaris.value[key].selected = !ahliWaris.value[key].selected
  if (!ahliWaris.value[key].selected) {
    ahliWaris.value[key].jumlah = 1
  }
}

// Modal functions
const openSyaratModal = () => {
  showSyaratModal.value = true
}

const closeSyaratModal = () => {
  showSyaratModal.value = false
}

// Family Tree functions
const toggleFamilyTree = () => {
  showFamilyTree.value = !showFamilyTree.value
}

const addFamilyMember = (type) => {
  const id = Date.now()
  const canvas = document.querySelector('.tree-canvas')
  const rect = canvas.getBoundingClientRect()
  
  familyTreeData.value.nodes.push({
    id,
    type,
    name: `${type} ${familyTreeData.value.nodes.length + 1}`,
    x: Math.random() * (rect.width - 100),
    y: Math.random() * (rect.height - 50),
    selected: false
  })
  
  syncFamilyTreeToAhliWaris()
}

const resetFamilyTree = () => {
  familyTreeData.value.nodes = []
  familyTreeData.value.connections = []
  
  // Reset semua ahli waris
  Object.keys(ahliWaris.value).forEach(key => {
    ahliWaris.value[key].selected = false
    ahliWaris.value[key].jumlah = 1
  })
}

// Drag and drop for family tree
const dragStart = (event, node) => {
  event.dataTransfer.setData('application/json', JSON.stringify(node))
}

const allowDrop = (event) => {
  event.preventDefault()
}

const drop = (event) => {
  event.preventDefault()
  const nodeData = JSON.parse(event.dataTransfer.getData('application/json'))
  const rect = event.target.getBoundingClientRect()
  const x = event.clientX - rect.left
  const y = event.clientY - rect.top
  
  // Update position
  const node = familyTreeData.value.nodes.find(n => n.id === nodeData.id)
  if (node) {
    node.x = Math.max(0, Math.min(x, rect.width - 100))
    node.y = Math.max(0, Math.min(y, rect.height - 50))
  }
}

// Sync family tree dengan ahli waris
const syncFamilyTreeToAhliWaris = () => {
  // Reset semua ahli waris
  Object.keys(ahliWaris.value).forEach(key => {
    ahliWaris.value[key].selected = false
    ahliWaris.value[key].jumlah = 1
  })
  
  // Hitung berdasarkan family tree
  const counts = {}
  familyTreeData.value.nodes.forEach(node => {
    const type = node.type.toLowerCase()
    counts[type] = (counts[type] || 0) + 1
  })
  
  // Set berdasarkan family tree
  if (counts.ayah && ahliWaris.value.ayah) {
    ahliWaris.value.ayah.selected = true
  }
  if (counts.ibu && ahliWaris.value.ibu) {
    ahliWaris.value.ibu.selected = true
  }
  if (counts.anak) {
    ahliWaris.value.anakLakiLaki.selected = true
    ahliWaris.value.anakLakiLaki.jumlah = counts.anak
  }
  if (counts.saudara) {
    ahliWaris.value.saudaraLakiLakiKandung.selected = true
    ahliWaris.value.saudaraLakiLakiKandung.jumlah = counts.saudara
  }
}

// Navigation functions
const setCurrentView = (view) => {
  currentView.value = view
}

const toggleSidebar = () => {
  sidebarOpen.value = !sidebarOpen.value
}

// Penjelasan Hukum functions
const showHukum = (hukumKey) => {
  selectedHukum.value = hukumDatabase[hukumKey]
  showHukumModal.value = true
}

const closeHukumModal = () => {
  showHukumModal.value = false
  selectedHukum.value = null
}

// Validasi wasiat
const validateWasiat = () => {
  const maxWasiat = hartaBersih.value / 3
  if (utangWasiat.value.wasiat > maxWasiat) {
    utangWasiat.value.wasiat = maxWasiat
  }
}
</script>

<template>
  <div class="app-container">
    <!-- Sidebar -->
    <div class="sidebar" :class="{ 'sidebar-open': sidebarOpen }">
      <div class="sidebar-header">
        <div class="logo">
          <span class="logo-icon">📚</span>
          <span class="logo-text">Faraidh</span>
        </div>
        <button @click="toggleSidebar" class="sidebar-toggle">
          {{ sidebarOpen ? '←' : '→' }}
        </button>
      </div>
      
      <nav class="sidebar-nav">
        <button 
          @click="setCurrentView('faraidh')" 
          :class="{ active: currentView === 'faraidh' }"
          class="nav-item"
        >
          <span class="nav-icon">⚖️</span>
          <span class="nav-text">Kalkulator Faraidh</span>
        </button>
        
        <button 
          @click="setCurrentView('utang-wasiat')" 
          :class="{ active: currentView === 'utang-wasiat' }"
          class="nav-item"
        >
          <span class="nav-icon">💰</span>
          <span class="nav-text">Utang & Wasiat</span>
        </button>
        
        <button 
          @click="setCurrentView('dashboard')" 
          :class="{ active: currentView === 'dashboard' }"
          class="nav-item"
        >
          <span class="nav-icon">📊</span>
          <span class="nav-text">Dashboard</span>
        </button>
        
        <div class="nav-divider"></div>
        
        <button @click="openSyaratModal" class="nav-item">
          <span class="nav-icon">📋</span>
          <span class="nav-text">Syarat-Syarat</span>
        </button>
        
        <button @click="toggleFamilyTree" class="nav-item">
          <span class="nav-icon">🌳</span>
          <span class="nav-text">Family Tree</span>
        </button>
      </nav>
    </div>

    <!-- Main Content -->
    <div class="main-content" :class="{ 'sidebar-open': sidebarOpen }">
      <!-- Header -->
      <div class="content-header">
        <h1 v-if="currentView === 'faraidh'">Kalkulator Faraidh</h1>
        <h1 v-if="currentView === 'utang-wasiat'">Kalkulator Utang & Wasiat</h1>
        <h1 v-if="currentView === 'dashboard'">Dashboard Visualisasi</h1>
        
        <p class="subtitle">Berdasarkan Kitab <span class="highlight">-</span></p>
        <p class="author">Karya -</p>
      </div>

      <!-- Content Area -->
      <div class="content-area">
        <!-- Faraidh Calculator View -->
        <div v-if="currentView === 'faraidh'" class="view-container">
          <!-- Input Tirkah -->
          <div class="section">
            <div class="section-title">
              <span class="icon-coin">💰</span>
              TIRKAH (HARTA BERSIH)
              <button @click="showHukum('bagianAyah')" class="help-btn" title="Lihat Penjelasan Hukum">
                ❓
              </button>
            </div>
            <div class="input-group">
              <span class="currency">Rp</span>
              <input 
                type="text" 
                :value="hartaBersih.toLocaleString('id-ID')"
                @input="handleHartaChange"
                class="harta-input"
              />
            </div>
            
            <!-- Info Harta Setelah Utang/Wasiat -->
            <div v-if="hartaBersihFinal !== hartaBersih" class="harta-info">
              <div class="harta-breakdown">
                <div class="breakdown-item">
                  <span>Harta Kotor:</span>
                  <span>{{ formatRupiah(hartaBersih) }}</span>
                </div>
                <div class="breakdown-item">
                  <span>Utang:</span>
                  <span>-{{ formatRupiah(utangWasiat.totalUtang) }}</span>
                </div>
                <div class="breakdown-item">
                  <span>Biaya Pemakaman:</span>
                  <span>-{{ formatRupiah(utangWasiat.biayaPemakaman) }}</span>
                </div>
                <div class="breakdown-item">
                  <span>Wasiat:</span>
                  <span>-{{ formatRupiah(utangWasiat.wasiat) }}</span>
                </div>
                <div class="breakdown-item total">
                  <span>Harta Bersih:</span>
                  <span>{{ formatRupiah(hartaBersihFinal) }}</span>
                </div>
              </div>
            </div>
          </div>

          <!-- Pilih Ahli Waris -->
          <div class="section">
            <div class="section-title">
              <span class="icon-people">👥</span>
              PILIH AHLI WARIS
              <button @click="showHukum('bagianSuami')" class="help-btn" title="Lihat Penjelasan Hukum">
                ❓
              </button>
            </div>
            
            <div class="ahli-waris-grid">
              <div 
                v-for="(info, key) in daftarAhliWaris" 
                :key="key"
                class="ahli-waris-item"
                :class="{ 
                  'selected': ahliWaris[key].selected, 
                  'terhalang': ahliWaris[key].terhalang 
                }"
              >
                <div class="checkbox-container">
                  <input 
                    type="checkbox" 
                    :id="key"
                    :checked="ahliWaris[key].selected"
                    @change="toggleAhliWaris(key)"
                    :disabled="ahliWaris[key].terhalang"
                  />
                  <label :for="key" class="ahli-label">
                    <div class="label-text">{{ info.label }}</div>
                    <div class="label-arab">{{ info.arab }}</div>
                  </label>
                </div>
                
                <!-- Input Jumlah -->
                <div v-if="ahliWaris[key].selected && info.canHaveMultiple" class="jumlah-input">
                  <label class="jumlah-label">Jumlah</label>
                  <input 
                    type="number" 
                    v-model.number="ahliWaris[key].jumlah"
                    min="1"
                    max="10"
                    class="jumlah-field"
                  />
                </div>
                
                <!-- Status Terhalang -->
                <div v-if="ahliWaris[key].terhalang" class="status-terhalang">
                  <span class="terhalang-icon">🚫</span>
                  <span class="terhalang-text">Terhalang (Mahjūb)</span>
                </div>
              </div>
            </div>
          </div>

          <!-- Hasil Perhitungan -->
          <div class="section" v-if="Object.keys(hasilPembagian).length > 0">
            <div class="section-title">
              <span class="icon-result">📊</span>
              HASIL PEMBAGIAN
              <button @click="showHukum('bagianAnak')" class="help-btn" title="Lihat Penjelasan Hukum">
                ❓
              </button>
            </div>
            
            <div class="hasil-grid">
              <div v-for="(nilai, ahli) in hasilPembagian" :key="ahli" class="hasil-item">
                <div class="ahli-info">
                  <div class="ahli-nama">{{ daftarAhliWaris[ahli]?.label || ahli }}</div>
                  <div class="ahli-arab">{{ daftarAhliWaris[ahli]?.arab }}</div>
                  <div v-if="ahliWaris[ahli]?.jumlah > 1" class="ahli-jumlah">
                    ({{ ahliWaris[ahli].jumlah }} orang)
                  </div>
                </div>
                <div class="ahli-bagian">
                  <div class="bagian-per-orang">{{ formatRupiah(nilai) }}</div>
                  <div v-if="ahliWaris[ahli]?.jumlah > 1" class="bagian-total">
                    Total: {{ formatRupiah(nilai * ahliWaris[ahli].jumlah) }}
                  </div>
                </div>
              </div>
            </div>
            
            <!-- Total Validasi -->
            <div class="validasi-total">
              <div class="total-terbagi">
                Total Terbagi: {{ formatRupiah(Object.values(hasilPembagian).reduce((sum, nilai, index) => {
                  const key = Object.keys(hasilPembagian)[index]
                  const jumlah = ahliWaris[key]?.jumlah || 1
                  return sum + (nilai * jumlah)
                }, 0)) }}
              </div>
              <div class="sisa-harta">
                Sisa: {{ formatRupiah(hartaBersihFinal - Object.values(hasilPembagian).reduce((sum, nilai, index) => {
                  const key = Object.keys(hasilPembagian)[index]
                  const jumlah = ahliWaris[key]?.jumlah || 1
                  return sum + (nilai * jumlah)
                }, 0)) }}
              </div>
            </div>
          </div>
        </div>

        <!-- Utang & Wasiat View -->
        <div v-if="currentView === 'utang-wasiat'" class="view-container">
          <div class="section">
            <div class="section-title">
              <span class="icon-coin">💰</span>
              HARTA KOTOR
            </div>
            <div class="input-group">
              <span class="currency">Rp</span>
              <input 
                type="text" 
                :value="hartaBersih.toLocaleString('id-ID')"
                @input="handleHartaChange"
                class="harta-input"
              />
            </div>
          </div>

          <div class="section">
            <div class="section-title">
              <span class="icon-debt">💳</span>
              UTANG & KEWAJIBAN
            </div>
            
            <div class="debt-grid">
              <div class="debt-item">
                <label class="debt-label">Total Utang</label>
                <div class="input-group">
                  <span class="currency">Rp</span>
                  <input 
                    type="number" 
                    v-model.number="utangWasiat.totalUtang"
                    class="debt-input"
                    placeholder="0"
                  />
                </div>
              </div>
              
              <div class="debt-item">
                <label class="debt-label">Biaya Pemakaman</label>
                <div class="input-group">
                  <span class="currency">Rp</span>
                  <input 
                    type="number" 
                    v-model.number="utangWasiat.biayaPemakaman"
                    class="debt-input"
                    placeholder="0"
                  />
                </div>
              </div>
            </div>
          </div>

          <div class="section">
            <div class="section-title">
              <span class="icon-wasiat">📝</span>
              WASIAT (MAKSIMAL 1/3 HARTA)
            </div>
            
            <div class="wasiat-container">
              <div class="input-group">
                <span class="currency">Rp</span>
                <input 
                  type="number" 
                  v-model.number="utangWasiat.wasiat"
                  @input="validateWasiat"
                  class="wasiat-input"
                  :class="{ 'invalid': !utangWasiat.wasiatValid }"
                  placeholder="0"
                />
              </div>
              
              <div class="wasiat-info">
                <div class="max-wasiat">
                  Maksimal Wasiat: {{ formatRupiah(hartaBersih / 3) }}
                </div>
                <div v-if="!utangWasiat.wasiatValid" class="wasiat-warning">
                  ⚠️ Wasiat melebihi batas syariat (1/3 harta)
                </div>
              </div>
            </div>
          </div>

          <div class="section">
            <div class="section-title">
              <span class="icon-result">📊</span>
              RINGKASAN PERHITUNGAN
            </div>
            
            <div class="summary-grid">
              <div class="summary-item">
                <div class="summary-label">Harta Kotor</div>
                <div class="summary-value">{{ formatRupiah(hartaBersih) }}</div>
              </div>
              <div class="summary-item expense">
                <div class="summary-label">Total Utang</div>
                <div class="summary-value">-{{ formatRupiah(utangWasiat.totalUtang) }}</div>
              </div>
              <div class="summary-item expense">
                <div class="summary-label">Biaya Pemakaman</div>
                <div class="summary-value">-{{ formatRupiah(utangWasiat.biayaPemakaman) }}</div>
              </div>
              <div class="summary-item expense">
                <div class="summary-label">Wasiat</div>
                <div class="summary-value">-{{ formatRupiah(utangWasiat.wasiat) }}</div>
              </div>
              <div class="summary-item total">
                <div class="summary-label">Harta Bersih untuk Waris</div>
                <div class="summary-value">{{ formatRupiah(hartaBersihFinal) }}</div>
              </div>
            </div>
          </div>
        </div>

        <!-- Dashboard View -->
        <div v-if="currentView === 'dashboard'" class="view-container">
          <div class="dashboard-grid">
            <div class="chart-section">
              <div class="section chart-section-inner">
                <div class="section-title">
                  <span class="icon-chart">📊</span>
                  VISUALISASI PEMBAGIAN WARIS
                </div>
                
                <div v-if="Object.keys(hasilPembagian).length > 0" class="chart-container">
                  <div class="chart-wrapper">
                    <Pie :data="chartData" :options="chartOptions" />
                  </div>
                </div>
                <div v-else class="no-data">
                  <div class="no-data-content">
                    <span class="no-data-icon">📈</span>
                    <h3>Belum Ada Data</h3>
                    <p>Pilih ahli waris di kalkulator faraidh untuk melihat visualisasi pembagian</p>
                    <button @click="setCurrentView('faraidh')" class="btn-goto-faraidh">
                      Pergi ke Kalkulator Faraidh
                    </button>
                  </div>
                </div>
              </div>
            </div>
            
            <div class="stats-section">
              <div class="section stats-section-inner">
                <div class="section-title">
                  <span class="icon-stats">📈</span>
                  STATISTIK PEMBAGIAN
                </div>
                
                <div class="stats-grid">
                  <div class="stat-card">
                    <div class="stat-icon">👥</div>
                    <div class="stat-value">{{ Object.keys(hasilPembagian).length }}</div>
                    <div class="stat-label">Ahli Waris</div>
                  </div>
                  
                  <div class="stat-card">
                    <div class="stat-icon">💰</div>
                    <div class="stat-value-money">{{ formatRupiah(hartaBersihFinal) }}</div>
                    <div class="stat-label">Harta Bersih</div>
                  </div>
                  
                  <div class="stat-card">
                    <div class="stat-icon">🔢</div>
                    <div class="stat-value">
                      {{ Object.values(hasilPembagian).reduce((sum, nilai, index) => {
                        const key = Object.keys(hasilPembagian)[index]
                        const jumlah = ahliWaris[key]?.jumlah || 1
                        return sum + jumlah
                      }, 0) }}
                    </div>
                    <div class="stat-label">Total Orang</div>
                  </div>
                  
                  <div class="stat-card">
                    <div class="stat-icon">📊</div>
                    <div class="stat-value">
                      {{ hartaBersihFinal > 0 ? Math.round((Object.values(hasilPembagian).reduce((sum, nilai, index) => {
                        const key = Object.keys(hasilPembagian)[index]
                        const jumlah = ahliWaris[key]?.jumlah || 1
                        return sum + (nilai * jumlah)
                      }, 0) / hartaBersihFinal) * 100) : 0 }}%
                    </div>
                    <div class="stat-label">Terbagi</div>
                  </div>
                </div>
              </div>
            </div>
          </div>
          
          <!-- Quick Summary -->
          <div class="section summary-overview">
            <div class="section-title">
              <span class="icon-summary">📋</span>
              RINGKASAN PEMBAGIAN
            </div>
            
            <div v-if="Object.keys(hasilPembagian).length > 0" class="summary-cards">
              <div v-for="(nilai, ahli) in hasilPembagian" :key="ahli" class="summary-card">
                <div class="summary-card-header">
                  <h4>{{ daftarAhliWaris[ahli]?.label || ahli }}</h4>
                  <span class="summary-arab">{{ daftarAhliWaris[ahli]?.arab }}</span>
                </div>
                <div class="summary-card-body">
                  <div class="summary-amount">{{ formatRupiah(nilai) }}</div>
                  <div v-if="ahliWaris[ahli]?.jumlah > 1" class="summary-detail">
                    × {{ ahliWaris[ahli].jumlah }} orang = {{ formatRupiah(nilai * ahliWaris[ahli].jumlah) }}
                  </div>
                </div>
              </div>
            </div>
            <div v-else class="no-summary">
              <p>Tidak ada data pembagian untuk ditampilkan</p>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Family Tree Modal -->
    <div v-if="showFamilyTree" class="modal-overlay" @click="toggleFamilyTree">
      <div class="modal-content family-tree-modal" @click.stop>
        <div class="modal-header">
          <h2>🌳 Family Tree Builder</h2>
          <button @click="toggleFamilyTree" class="modal-close">✕</button>
        </div>
        
        <div class="modal-body">
          <div class="family-tree-toolbar">
            <button @click="addFamilyMember('Ayah')" class="tree-btn">
              👨 Tambah Ayah
            </button>
            <button @click="addFamilyMember('Ibu')" class="tree-btn">
              👩 Tambah Ibu
            </button>
            <button @click="addFamilyMember('Anak')" class="tree-btn">
              👶 Tambah Anak
            </button>
            <button @click="addFamilyMember('Saudara')" class="tree-btn">
              👫 Tambah Saudara
            </button>
            <button @click="resetFamilyTree" class="tree-btn tree-btn-danger">
              🗑️ Reset
            </button>
          </div>
          
          <div 
            class="tree-canvas" 
            @drop="drop" 
            @dragover="allowDrop"
          >
            <div 
              v-for="node in familyTreeData.nodes" 
              :key="node.id"
              class="tree-node"
              :style="{ left: node.x + 'px', top: node.y + 'px' }"
              draggable="true"
              @dragstart="dragStart($event, node)"
            >
              <div class="node-content">
                <div class="node-type">{{ node.type }}</div>
                <div class="node-name">{{ node.name }}</div>
              </div>
            </div>
          </div>
          
          <div class="family-tree-info">
            <p><strong>Cara Penggunaan:</strong></p>
            <ul>
              <li>Klik tombol untuk menambah anggota keluarga</li>
              <li>Drag & drop untuk mengatur posisi</li>
              <li>Sistem otomatis akan sync dengan ahli waris</li>
              <li>Gunakan reset untuk mulai dari awal</li>
            </ul>
          </div>
        </div>
      </div>
    </div>

    <!-- Modal Syarat-Syarat -->
    <div v-if="showSyaratModal" class="modal-overlay" @click="closeSyaratModal">
      <div class="modal-content syarat-modal" @click.stop>
        <div class="modal-header">
          <h2>📋 Syarat-Syarat Waris dalam Islam</h2>
          <button @click="closeSyaratModal" class="modal-close">✕</button>
        </div>
        
        <div class="modal-body">
          <div class="syarat-section">
            <h3>🔑 Syarat-Syarat Pokok Warisan</h3>
            <ol>
              <li><strong>Mawt (Kematian):</strong> Pewaris benar-benar telah meninggal dunia, baik secara hakiki (nyata) maupun hukmi (menurut hukum).</li>
              <li><strong>Hayat (Kehidupan):</strong> Ahli waris masih hidup pada saat pewaris meninggal dunia.</li>
              <li><strong>Ilmu bi Jihah al-Irth (Mengetahui Sebab Warisan):</strong> Diketahui sebab-sebab yang menjadikan seseorang berhak mendapat warisan.</li>
            </ol>
          </div>

          <div class="syarat-section">
            <h3>🚫 Penghalang Warisan (Mawāni' al-Irth)</h3>
            <ol>
              <li><strong>Qatl (Pembunuhan):</strong> Ahli waris yang membunuh pewaris tidak berhak mendapat warisan.</li>
              <li><strong>Ikhtilāf ad-Dīn (Perbedaan Agama):</strong> Muslim tidak mewarisi non-Muslim, begitu juga sebaliknya.</li>
              <li><strong>Riqq (Perbudakan):</strong> Budak tidak dapat mewarisi dari tuannya (dalam konteks sejarah).</li>
            </ol>
          </div>

          <div class="syarat-section">
            <h3>📊 Urutan Prioritas Ahli Waris</h3>
            <div class="priority-list">
              <div class="priority-item">
                <strong>1. Ashāb al-Furūd (Ahli Waris yang Mendapat Bagian Pasti)</strong>
                <small>Suami, istri, ayah, ibu, anak perempuan, saudara perempuan, dll.</small>
              </div>
              <div class="priority-item">
                <strong>2. 'Asabah (Ahli Waris Sisa)</strong>
                <small>Anak laki-laki, ayah (jika tidak ada anak), saudara laki-laki, dll.</small>
              </div>
              <div class="priority-item">
                <strong>3. Dzawī al-Arhām (Kerabat Jauh)</strong>
                <small>Jika tidak ada ashāb al-furūd dan 'asabah</small>
              </div>
            </div>
          </div>

          <div class="syarat-section">
            <h3>📐 Bagian-Bagian Pokok (Furūd Muqaddarah)</h3>
            <div class="bagian-grid">
              <div class="bagian-item">1/2</div>
              <div class="bagian-item">1/3</div>
              <div class="bagian-item">1/4</div>
              <div class="bagian-item">1/6</div>
              <div class="bagian-item">1/8</div>
              <div class="bagian-item">2/3</div>
            </div>
          </div>

          <div class="syarat-section">
            <h3>⚖️ Prinsip Keadilan Islam</h3>
            <p>Pembagian warisan dalam Islam didasarkan pada prinsip keadilan yang telah ditetapkan Allah SWT dalam Al-Quran. 
            Setiap ahli waris mendapat bagian sesuai dengan kedudukan, tanggung jawab, dan kewajiban mereka dalam keluarga.</p>
          </div>
        </div>
        
        <div class="modal-footer">
          <button @click="closeSyaratModal" class="btn-close">Tutup</button>
        </div>
      </div>
    </div>

    <!-- Modal Penjelasan Hukum -->
    <div v-if="showHukumModal && selectedHukum" class="modal-overlay" @click="closeHukumModal">
      <div class="modal-content hukum-modal" @click.stop>
        <div class="modal-header">
          <h2>📖 {{ selectedHukum.title }}</h2>
          <button @click="closeHukumModal" class="modal-close">✕</button>
        </div>
        
        <div class="modal-body">
          <div class="hukum-section">
            <h3>🕌 Ayat Al-Quran</h3>
            <div class="ayat-container">
              <div class="ayat-arab">{{ selectedHukum.ayat }}</div>
              <div class="ayat-terjemah">{{ selectedHukum.terjemah }}</div>
              <div class="ayat-surat">{{ selectedHukum.surat }}</div>
            </div>
          </div>
          
          <div class="hukum-section">
            <h3>📝 Penjelasan</h3>
            <p>{{ selectedHukum.penjelasan }}</p>
          </div>
          
          <div class="hukum-section">
            <h3>💡 Contoh Kasus</h3>
            <ul class="contoh-list">
              <li v-for="contoh in selectedHukum.contohKasus" :key="contoh">{{ contoh }}</li>
            </ul>
          </div>
        </div>
        
        <div class="modal-footer">
          <button @click="closeHukumModal" class="btn-close">Tutup</button>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
* {
  box-sizing: border-box;
}

/* App Layout */
.app-container {
  display: flex;
  min-height: 100vh;
  background: linear-gradient(135deg, #f0f9ff 0%, #e0f2fe 100%);
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
}

/* Sidebar Styles */
.sidebar {
  width: 80px;
  background: white;
  box-shadow: 2px 0 10px rgba(0, 0, 0, 0.1);
  transition: all 0.3s ease;
  position: fixed;
  height: 100vh;
  z-index: 100;
  overflow: hidden;
}

.sidebar.sidebar-open {
  width: 280px;
}

.sidebar-header {
  padding: 20px;
  border-bottom: 2px solid #f3f4f6;
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.logo {
  display: flex;
  align-items: center;
  gap: 12px;
}

.logo-icon {
  font-size: 2rem;
  background: linear-gradient(135deg, #10b981, #059669);
  -webkit-background-clip: text;
  background-clip: text;
  -webkit-text-fill-color: transparent;
}

.logo-text {
  font-size: 1.5rem;
  font-weight: 700;
  color: #1f2937;
  opacity: 0;
  transition: opacity 0.3s ease;
}

.sidebar.sidebar-open .logo-text {
  opacity: 1;
}

.sidebar-toggle {
  background: #10b981;
  color: white;
  border: none;
  border-radius: 50%;
  width: 32px;
  height: 32px;
  cursor: pointer;
  font-size: 1rem;
  transition: all 0.2s;
}

.sidebar-toggle:hover {
  background: #059669;
  transform: scale(1.1);
}

/* Navigation */
.sidebar-nav {
  padding: 20px 0;
}

.nav-item {
  width: 100%;
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 15px 20px;
  background: none;
  border: none;
  cursor: pointer;
  transition: all 0.2s;
  text-align: left;
  color: #6b7280;
}

.nav-item:hover {
  background: #f3f4f6;
  color: #374151;
}

.nav-item.active {
  background: #ecfdf5;
  color: #059669;
  border-right: 3px solid #10b981;
}

.nav-icon {
  font-size: 1.2rem;
  min-width: 24px;
}

.nav-text {
  font-weight: 500;
  opacity: 0;
  transition: opacity 0.3s ease;
  white-space: nowrap;
}

.sidebar.sidebar-open .nav-text {
  opacity: 1;
}

.nav-divider {
  height: 1px;
  background: #f3f4f6;
  margin: 10px 0;
}

/* Main Content */
.main-content {
  flex: 1;
  padding: 20px;
  margin-left: 80px;
  transition: margin-left 0.3s ease;
}

.main-content.sidebar-open {
  margin-left: 280px;
}

.content-header {
  margin-bottom: 20px;
}

.content-header h1 {
  font-size: 1.8rem;
  font-weight: 600;
  color: #111827;
}

.subtitle {
  font-size: 1rem;
  color: #6b7280;
  margin-top: 5px;
}

.author {
  font-size: 0.875rem;
  color: #6b7280;
  margin-top: 2px;
}

/* Section Styles */
.section {
  margin-bottom: 20px;
  padding: 15px;
  background: white;
  border-radius: 8px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

.section-title {
  font-size: 1.25rem;
  font-weight: 500;
  color: #111827;
  margin-bottom: 10px;
  display: flex;
  align-items: center;
  gap: 8px;
}

.icon-coin, .icon-people, .icon-result, .icon-chart, .icon-debt, .icon-wasiat {
  font-size: 1.5rem;
  color: #10b981;
}

.section-title button {
  background: none;
  border: none;
  cursor: pointer;
  color: #6b7280;
  transition: color 0.2s;
}

.section-title button:hover {
  color: #10b981;
}

.input-group {
  display: flex;
  align-items: center;
  border: 1px solid #d1d5db;
  border-radius: 8px;
  overflow: hidden;
}

.input-group span.currency {
  background: #f3f4f6;
  padding: 10px;
  font-weight: 500;
  color: #111827;
}

.input-group input {
  flex: 1;
  padding: 10px;
  border: none;
  outline: none;
  font-size: 1rem;
  color: #111827;
}

.input-group input::placeholder {
  color: #9ca3af;
}

/* Ahli Waris Grid */
.ahli-waris-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(120px, 1fr));
  gap: 10px;
}

.ahli-waris-item {
  background: #f9fafb;
  border: 1px solid #d1d5db;
  border-radius: 8px;
  padding: 10px;
  position: relative;
}

.ahli-waris-item.selected {
  background: #ecfdf5;
  border-color: #10b981;
}

.ahli-waris-item.terhalang {
  opacity: 0.5;
}

.checkbox-container {
  display: flex;
  align-items: center;
  gap: 8px;
}

.checkbox-container input {
  width: 20px;
  height: 20px;
  cursor: pointer;
}

.label-text {
  font-weight: 500;
  color: #111827;
}

.label-arab {
  font-size: 0.875rem;
  color: #6b7280;
}

/* Hasil Pembagian */
.hasil-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(150px, 1fr));
  gap: 10px;
}

.hasil-item {
  background: #f9fafb;
  border: 1px solid #d1d5db;
  border-radius: 8px;
  padding: 10px;
}

.ahli-info {
  margin-bottom: 10px;
}

.ahli-nama {
  font-weight: 500;
  color: #111827;
}

.ahli-arab {
  font-size: 0.875rem;
  color: #6b7280;
}

.ahli-jumlah {
  font-size: 0.875rem;
  color: #10b981;
  position: absolute;
  top: 10px;
  right: 10px;
}

.bagian-per-orang {
  font-weight: 500;
  color: #111827;
}

.bagian-total {
  font-size: 0.875rem;
  color: #6b7280;
}

/* Validasi Total */
.validasi-total {
  display: flex;
  justify-content: space-between;
  padding: 10px;
  background: #f3f4f6;
  border-radius: 8px;
  margin-top: 10px;
}

.total-terbagi, .sisa-harta {
  font-weight: 500;
  color: #111827;
}

/* Chart Styles */
.chart-container {
  position: relative;
  width: 100%;
  height: 300px;
}

.no-data {
  text-align: center;
  padding: 50px 0;
  color: #6b7280;
}

.no-data-icon {
  font-size: 3rem;
  display: block;
  margin-bottom: 10px;
}

.btn-goto-faraidh {
  background: #10b981;
  color: white;
  padding: 10px 20px;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  transition: background 0.2s;
}

.btn-goto-faraidh:hover {
  background: #059669;
}

/* Family Tree Styles */
.tree-canvas {
  position: relative;
  width: 100%;
  height: 400px;
  border: 1px dashed #d1d5db;
  border-radius: 8px;
  overflow: hidden;
  margin-top: 10px;
}

.tree-node {
  position: absolute;
  background: #ecfdf5;
  border: 1px solid #10b981;
  border-radius: 8px;
  padding: 10px;
  cursor: move;
  transition: transform 0.2s;
}

.tree-node:hover {
  transform: scale(1.05);
}

.node-content {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.node-type {
  font-size: 0.875rem;
  color: #6b7280;
}

.node-name {
  font-weight: 500;
  color: #111827;
}

/* Modal Styles */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(0, 0, 0, 0.7);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 200;
}

.modal-content {
  background: white;
  border-radius: 8px;
  overflow: hidden;
  width: 90%;
  max-width: 600px;
  box-shadow: 0 4px 20px rgba(0, 0, 0, 0.1);
}

.modal-header {
  padding: 15px;
  background: #10b981;
  color: white;
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.modal-title {
  font-size: 1.25rem;
  font-weight: 500;
}

.modal-close {
  background: none;
  border: none;
  color: white;
  cursor: pointer;
  font-size: 1.5rem;
}

.modal-body {
  padding: 15px;
}

.modal-footer {
  padding: 10px;
  display: flex;
  justify-content: flex-end;
}

.btn-close {
  background: #10b981;
  color: white;
  padding: 10px 20px;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  transition: background 0.2s;
}

.btn-close:hover {
  background: #059669;
}

/* Responsive Styles */
@media (max-width: 768px) {
  .sidebar {
    width: 100%;
    height: auto;
    position: relative;
  }

  .sidebar.sidebar-open {
    width: 100%;
  }

  .main-content {
    margin-left: 0;
  }

  .tree-canvas {
    height: 300px;
  }
}
</style>

