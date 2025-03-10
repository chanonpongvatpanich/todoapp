<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref('personal')

const todos_asc = computed(() => [...todos.value].sort((a, b) => a.createdAt - b.createdAt))

watch(name, (newVal) => {
	localStorage.setItem('name', newVal)
})

watch(todos, (newVal) => {
	localStorage.setItem('todos', JSON.stringify(newVal))
}, {
	deep: true
})

const addTodo = () => {
	if (input_content.value.trim() === '' || !input_category.value) {
		return
	}

	todos.value.push({
		content: input_content.value,
		done: false,
		createdAt: Date.now()
	})

}

const removeTodo = (todo) => {
	todos.value = todos.value.filter((t) => t !== todo)
}

onMounted(() => {
	name.value = localStorage.getItem('name') || ''
	todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
	<main class="app">
		<section class="greeting">
			<h1 class="title">
				What's up, <input type="text" id="name" placeholder="Name here" v-model="name">
			</h1>
		</section>

		<section class="create-todo">
			<h2>CREATE A TODO</h2>
			<form id="new-todo-form" @submit.prevent="addTodo">
				<h3>What's on your todo list?</h3>
				<input
					type="text"
					name="content"
					id="content"
					placeholder="e.g. make a video"
					v-model="input_content"
				/>

				<input type="submit" value="Add todo" />
			</form>
		</section>

		<section class="todo-list">
			<h3>TODO LIST</h3>
			<div class="list" id="todo-list">
				<div v-for="todo in todos_asc" :key="todo.createdAt" :class="`todo-item ${todo.done ? 'done' : ''}`">
					<label>
						<input type="checkbox" v-model="todo.done" />
						<span :class="`bubble ${todo.category}`"></span>
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
