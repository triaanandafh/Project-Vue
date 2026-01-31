<script setup>
import { ref } from 'vue'
const titleInput = ref('')
const categoryInput = ref('General')

// daftar rencana foto
const photoProjects = ref([
  
])

// fungsi tambah rencana
const addNewProject = () => {
  if (titleInput.value !== '') {
    const newPlan = {
      id: Date.now(),
      title: titleInput.value,
      category: categoryInput.value || 'General',
      isDone: false
    }
    photoProjects.value.push(newPlan)
    titleInput.value = ''
    categoryInput.value = 'General'
  }
}
</script>

<template>
 <div class="main-container">
  <h1>Photo Planner</h1>
  <form class="form-box" @submit.prevent="addNewProject">
    <input v-model="titleInput"
    @keyup.enter="addNewProject" placeholder="Write your ideas here...">
    <select v-model="categoryInput" >
      <option value="General">General</option>
      <option value="Family">Family</option>
      <option value="Graduation">Graduation</option>
      <option value="Wedding">Wedding</option>
      <option value="Urban">Urban</option>
    </select>
    <button type="submit">Add Project</button>
  </form>

  <hr>

  <div class="list-container">
    <div v-for="item in photoProjects" :key="item.id" class="list-item">
      <h3>{{ item.title }}</h3>
      <p>Kategori: {{ item.category }}</p>

      <label for="">
        <input type="checkbox" v-model="item.isDone">
        {{ item.isDone ? 'Selesai' : 'Rencana' }}
      </label>
    </div>
  </div>

 </div>
</template>

<style scoped>
/* Container Utama */
.main-container {
  max-width: 600px;
  margin: 40px auto;
  padding: 20px;
  font-family: 'Inter', sans-serif;
  color: #333;
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
  background: white;
  border: 1px solid #eee;
  padding: 15px;
  border-radius: 10px;
  transition: transform 0.2s;
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
  background: #edf2f7;
  margin-bottom: 10px;
}
</style>
