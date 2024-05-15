<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)

const todos_asc = computed(() => todos.value.sort((a,b) =>{
	return a.createdAt - b.createdAt
}))

watch(name, (newVal) => {
	localStorage.setItem('name', newVal)
})

watch(todos, (newVal) => {
	localStorage.setItem('todos', JSON.stringify(newVal))
}, {
	deep: true
})

const addTodo = () => {
	if (input_content.value.trim() === '' || input_category.value === null) {
		return
	}

	todos.value.push({
		content: input_content.value,
		category: input_category.value,
		done: false,
		editable: false,
		createdAt: new Date().getTime()
	})
}

const removeTodo = (todo) => {
	todos.value = todos.value.filter((t) => t !== todo)
}

const updateTodo = (todo) => {
    todo.editable = false;
}

onMounted(() => {
	name.value = localStorage.getItem('name') || ''
	todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
	<main class="app">
	  
	  <section class="greeting">
		<h2 class="title">
		  Hello, <input type="text" id="name" placeholder="Your Name" v-model="name">
		</h2>
	  </section>
  
	  <section class="create-todo">
		<h3>Membuat Sebuah Todo</h3>
  
		<form id="new-todo-form" @submit.prevent="addTodo">
		  <h4>Apa yang ada dalam daftar tugas anda?</h4>
		  <input 
			type="text" 
			name="content" 
			id="content" 
			placeholder="Contohnya membuat LAPRAK"
			v-model="input_content" />
		  
		  <h4>Pilih Kategori</h4>
		  <div class="options">
  
			<label>
			  <input 
				type="radio" 
				name="category" 
				id="category1" 
				value="group"
				v-model="input_category" />
			  <span class="bubble group"></span>
			  <div>Group</div>
			</label>
  
			<label>
			  <input 
				type="radio" 
				name="category" 
				id="category2" 
				value="personal"
				v-model="input_category" />
			  <span class="bubble personal"></span>
			  <div>Personal</div>
			</label>
  
		  </div>
  
		  <input type="submit" value="Add todo" />
		</form>
	  </section>
  
	  <section class="todo-list">
		<h3>TODO LIST</h3>
		<div class="list" id="todo-list">
  
		  <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
			<label>
			  <input type="checkbox" v-model="todo.done" />
			  <span :class="`bubble ${
				todo.category == 'group' 
				  ? 'group' 
				  : 'personal'
			  }`"></span>
			</label>
  
			<div class="todo-content">
			  <input type="text" v-model="todo.content" />
			</div>
  
			<div class="actions">
			  <button class="delete" @click="removeTodo(todo)">Delete</button>
			  <button class="update" @click="updateTodo(todo)">Update</button>
			</div>
		  </div>
  
		</div>
	  </section>
  
	</main>
  </template>
