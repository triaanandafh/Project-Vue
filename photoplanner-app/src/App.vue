<script setup>
import { ref, computed, watch, onMounted } from 'vue'
const titleInput = ref('')
const categoryInput = ref('General')
const locationInput = ref('')
const descInput = ref('')
const dateInput = ref('')

const searchQuery = ref('')
const selectedCategory = ref('All')
// daftar rencana foto
const photoProjects = ref([
  
])

// LOGIKA STATISTIK
// Menghitung total rencana foto
const totalProjects = computed(() => photoProjects.value.length)

// Menghitung yang sudah berstatus 'Selesai'
const completedProjects = computed(() => {
  return photoProjects.value.filter(p => p.isDone).length
})

// Menghitung persentase progres (mirip logika di Java/PHP)
const progressPercentage = computed(() => {
  if (totalProjects.value === 0) return 0
  return Math.round((completedProjects.value / totalProjects.value) * 100)
})


//Fitur localStorage
// 1. ambil data (saat aplikasi pertama dimuat)
onMounted(() => {
  const savedProjects = localStorage.getItem('photoProjects')
  if (savedProjects) {
    photoProjects.value = JSON.parse(savedProjects)
  }
})
// 2. simpan data ke localStorage setiap ada perubahan pada photoProjects
watch(photoProjects, (newProjects) => {
  localStorage.setItem('photoProjects', JSON.stringify(newProjects))
}, { deep: true })

// fungsi add plan
const addNewProject = () => {
  if (titleInput.value !== '') {
    const newPlan = {
      id: Date.now(),
      title: titleInput.value,
      category: categoryInput.value || 'General',
      location: locationInput.value,
      description: descInput.value,
      date: dateInput.value,
      isDone: false
    }
    photoProjects.value.push(newPlan)
    titleInput.value = ''
    categoryInput.value = 'General'
  }
}

const filteredProjects = computed(() => {
  return photoProjects.value.filter((project) => {
    const matchSearch = project.title.toLowerCase().includes(searchQuery.value.toLowerCase())
    const matchCategory = selectedCategory.value === 'All' || project.category === selectedCategory.value
    return matchSearch && matchCategory
  })
})

const deleteProject = (id) => {
  if(confirm('Are you sure you want to delete this project?')) {
    photoProjects.value = photoProjects.value.filter(project => project.id !== id)
  }
}
</script>

<template>
 
  <div class="main-container">
    <h1>Photo Planner</h1>

    <div class="dashboard-wrapper">
      
      <aside class="sidebar-input">
        <div class="stats-container">
          <div class="stat-card">
            <span class="stat-label">Total Projects</span>
            <span class="stat-value">{{ totalProjects }}</span>
          </div>
          <div class="stat-card">
            <span class="stat-label">Completed</span>
            <span class="stat-value">{{ completedProjects }}</span>
          </div>
          <div class="stat-card">
            <span class="stat-label">Progress</span>
            <span class="stat-value">{{ progressPercentage }}%</span>
          </div>
        </div>

        <form class="form-box" @submit.prevent="addNewProject">
          <h3>New Project</h3>
          <input v-model="titleInput" placeholder="Write your ideas here...">
          <select v-model="categoryInput">
            <option value="General">General</option>
            <option value="Family">Family</option>
            <option value="Graduation">Graduation</option>
            <option value="Urban">Urban</option>
            <option value="Wedding">Wedding</option>
          </select>
          <input v-model="locationInput" placeholder="Location">
          <input v-model="descInput" placeholder="Description">
          <input v-model="dateInput" type="date">
          <button type="submit">Add Project</button>
        </form>
      </aside>

      <main class="main-content">
        <div class="search-filter-section">
          <input v-model="searchQuery" placeholder="Cari judul project..." class="search-bar">
          <div class="category-tabs">
            <button v-for="cat in ['All', 'Nature', 'Urban', 'Graduation', 'Family']" 
                    :key="cat" @click="selectedCategory = cat"
                    :class="{ 'active-tab': selectedCategory === cat }">
              {{ cat }}
            </button>
          </div>
        </div>

        <div class="list-container">
         <div v-for="item in filteredProjects" :key="item.id" class="card">
  <div class="card-header">
    <span class="category-tag">{{ item.category }}</span>
    <button @click="deleteProject(item.id)" class="btn-delete-circle">&times;</button>
  </div>

  <h3 :class="{ 'coret': item.isDone }">{{ item.title }}</h3>

  <div class="card-info">
    <p v-if="item.date" class="info-item">
      <span>📅</span> {{ item.date }}
    </p>
    <p v-if="item.location" class="info-item">
      <span>📍</span> {{ item.location }}
    </p>
    <p v-if="item.description" class="info-desc">
      {{ item.description }}
    </p>
  </div>

  <div class="card-action">
    <label>
      <input type="checkbox" v-model="item.isDone">
      {{ item.isDone ? 'Selesai' : 'Rencana' }}
    </label>
  </div>
</div>
        </div>
      </main>

    </div>
  </div>
</template>

<style scoped>
/* Container Utama */
.main-container {
  max-width: 900px;
  margin: 40px auto;
  padding: 20px;
  font-family: 'Inter', sans-serif;
  color: #333;
}
.dashboard-wrapper {
  display: flex;
  gap: 30px;
  align-items: flex-start; /* Agar sidebar tidak ikut memanjang ke bawah */
}
.sidebar-input {
  flex: 0 0 280px; 
  display: flex;
  flex-direction: column;
  gap: 20px;
  position: sticky;
  top: 20px;
}
.main-content {
  flex: 2; /* Mengambil 2 bagian (lebih lebar) */
}

.btn-delete-circle {
  position: absolute; /* Taruh di pojok kartu */
  top: 10px;
  right: 10px;
  width: 25px;
  height: 25px;
  border-radius: 50%;
  border: none;
  background-color: #ffeded;
  color: #ff4d4f;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 18px;
  transition: 0.2s;
}

.btn-delete-circle:hover {
  background-color: #ff4d4f;
  color: white;
}

/* Responsif untuk HP */
@media (max-width: 768px) {
  .dashboard-wrapper {
    flex-direction: column;
  }
  .sidebar-input {
    position: static;
    width: 100%;
  }
}

/* Judul */
h1 {
  text-align: center;
  color: #2c3e50;
  font-weight: 700;
  letter-spacing: -1px;
}

/* Kotak Input (Form) */
.form-box {
  background: #f8f9fa;
  padding: 20px;
  border-radius: 12px;
  display: flex;
  flex-direction: column;
  gap: 12px;
  margin-bottom: 30px;
  box-shadow: 0 4px 6px rgba(0,0,0,0.05);
}

input, select, button {
  padding: 12px;
  border-radius: 8px;
  border: 1px solid #ddd;
  font-size: 16px;
}

button {
  background-color: #42b883; /* Warna Khas Vue */
  color: white;
  border: none;
  font-weight: bold;
  cursor: pointer;
  transition: 0.3s;
}

button:hover {
  background-color: #35495e;
}

/* Grid Kartu */
.list-container {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 15px;
}

/* Kartu Project */
.card {
  position: relative;
  background: #f8f9fa;
  border: 1px solid #eee;
  padding: 20px;
  border-radius: 12px;
}

.card:hover {
  transform: translateY(-5px);
  box-shadow: 0 10px 15px rgba(0,0,0,0.1);
}

/* Efek Coret untuk yang sudah Done */
.coret {
  text-decoration: line-through;
  color: #a0aec0;
}

/* Badge Kategori */
.category-tag {
  display: inline-block;
  padding: 4px 8px;
  border-radius: 4px;
  font-size: 12px;
  background: #faf9de;
  margin-bottom: 10px;
}

/* seacrh bar dan tabs */
/* Container untuk Search dan Tabs */
.search-filter-section {
  margin-bottom: 25px;
  display: flex;
  flex-direction: column;
  gap: 15px;
}

/* Styling Search Bar */
.search-bar {
  width: 100%;
  padding: 12px 15px;
  border: 2px solid #edf2f7;
  border-radius: 10px;
  font-size: 15px;
  transition: border-color 0.3s;
  outline: none;
}

.search-bar:focus {
  border-color: #42b883; /* Hijau Vue saat diklik */
}

/* Container Tabs */
.category-tabs {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
}

/* Styling Tombol Tab */
.category-tabs button {
  padding: 8px 16px;
  border: 1px solid #e2e8f0;
  background: white;
  color: #4a5568;
  border-radius: 20px; /* Membuat lonjong/pill-shaped */
  font-size: 14px;
  cursor: pointer;
  transition: all 0.2s ease;
}

.category-tabs button:hover {
  background: #f7fafc;
  border-color: #cbd5e0;
}

/* Tab yang sedang Aktif (Selected) */
.category-tabs button.active-tab {
  background: #35495e; /* Biru gelap Vue */
  color: white;
  border-color: #35495e;
  font-weight: 600;
  box-shadow: 0 4px 6px rgba(0,0,0,0.1);
}

/* CSS STATISTIK */
.stats-container {
  display: grid;
  grid-template-columns: 1fr; /* Berjejer ke bawah */
  gap: 10px;
  background: #fff;
  padding: 15px;
  border-radius: 12px;
  border: 1px solid #eee;
}

.stat-card {
  padding: 8px 12px;
  border-left: 4px solid #42b883;
  background: #e0f4e674;/* Kasih aksen warna Vue */
}

.stat-label {
  display: block;
  font-size: 12px;
  color: #718096;
  text-transform: uppercase;
  margin-bottom: 5px;
}

.stat-value {
  font-size: 24px;
  font-weight: bold;
  color: #2d3748;
}

</style>
