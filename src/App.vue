<script setup>
import { ref, onMounted, computed, watch } from 'vue'

// State for storing todo items
const todos = ref([])

// State for storing the user's name
const name = ref('')

// States for the input fields in the todo creation form
const input_content = ref('')
const input_category = ref(null)

// Computed property to sort todos in ascending order based on creation time
const todos_asc = computed(() => todos.value.sort((a,b) => a.createdAt - b.createdAt))

// Watcher to save the user's name to local storage whenever it changes
watch(name, (newVal) => {
	localStorage.setItem('name', newVal)
})

// Watcher to save the todos to local storage whenever they change
watch(todos, (newVal) => {
	localStorage.setItem('todos', JSON.stringify(newVal))
}, {
	deep: true
})

// Function to add a new todo item
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

// Function to remove a todo item
const removeTodo = (todo) => {
	todos.value = todos.value.filter((t) => t !== todo)
}

// Lifecycle hook to load the user's name and todos from local storage when the component is mounted
onMounted(() => {
	name.value = localStorage.getItem('name') || ''
	todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
	<main class="app">
		
		<!-- Greeting section -->
		<section class="greeting">
			<h2 class="title">
				Hello, <input type="text" id="name" placeholder="Name here" v-model="name">
			</h2>
		</section>

		<!-- Todo creation form -->
		<section class="create-todo">
			<h3>CREATE A TODO</h3>
			<form id="new-todo-form" @submit.prevent="addTodo">
				<h4>What's on your todo list?</h4>
				<input 
					type="text" 
					name="content" 
					id="content" 
					placeholder="e.g. make an app"
					v-model="input_content" />
				
				<h4>Pick a category</h4>
				<div class="options">
					<label>
						<input 
							type="radio" 
							name="category" 
							id="category1" 
							value="business"
							v-model="input_category" />
						<span class="bubble business"></span>
						<div>Business</div>
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
				<input type="submit" value="Add to the list" />
			</form>
		</section>

		<!-- Todo list display -->
		<section class="todo-list">
			<h3>TODO LIST</h3>
			<div class="list" id="todo-list">
        <div v-for="todo in todos_asc" :key="todo.createdAt" :class="`todo-item ${todo.done && 'done'}`">
					<label>
						<input type="checkbox" v-model="todo.done" />
						<span :class="`bubble ${
							todo.category == 'business' 
								? 'business' 
								: 'personal'
						}`"></span>
					</label>
					<div class="todo-content">
						<input type="text" v-model="todo.content" />
					</div>
					<div class="actions">
						<button class="delete" @click="removeTodo(todo)">Delete</button>
					</div>
				</div>
			</div>
		</section>
	</main>
</template>
