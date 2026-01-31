<script setup>
import { ref, computed } from 'vue'

// State (React: useState)
const newTask = ref('')
const todos = ref([])

// Method untuk tambah data
const addTodo = () => {
  if (newTask.value.trim()) {
    todos.value.push({
      id: Date.now(),
      text: newTask.value,
      done: false
    })
    newTask.value = ''
  }
}

// Method untuk hapus
const removeTodo = (id) => {
  todos.value = todos.value.filter(t => t.id !== id)
}

// Computed (React: useMemo) - Otomatis update kalau todos berubah
const pendingTasks = computed(() => {
  return todos.value.filter(t => !t.done).length
})
</script>


<template>
  <div class="container">
    <h1>List Tugas Aku</h1>
    
    <div class="input-group">
      <input 
        v-model="newTask" 
        @keyup.enter="addTodo"          
        
        placeholder="Mau ngerjain apa hari ini?"
      >
      <button @click="addTodo">Tambah</button>
    </div>

    <div v-if="todos.length > 0">
  <p>Sisa tugas: <strong>{{ pendingTasks }}</strong></p>

  <ul>
    <li v-for="todo in todos" :key="todo.id">
      <input type="checkbox" v-model="todo.done">
      <span :class="{ 'coret': todo.done }">{{ todo.text }}</span>
      <button @click="removeTodo(todo.id)" class="btn-del">Hapus</button>
    </li>
  </ul>
</div>

<p v-else>Belum ada tugas, santai dulu! ☕</p>
  </div>
</template>


<style scoped>
.container { max-width: 400px; margin: 50px auto; font-family: sans-serif; }
.input-group { display: flex; gap: 10px; margin-bottom: 20px; }
input { flex: 1; padding: 8px; }
.coret { text-decoration: line-through; color: #888; }
.btn-del { margin-left: auto; background: #ff4d4d; color: white; border: none; cursor: pointer; }
ul { list-style: none; padding: 0; }
li { display: flex; align-items: center; gap: 10px; margin-bottom: 10px; padding: 8px; border-bottom: 1px solid #eee; }
</style>