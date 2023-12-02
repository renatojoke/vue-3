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

onMounted(() => {
	name.value = localStorage.getItem('name') || ''
	todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
	<main class="app">
		
		<section class="greeting">
			<h2 class="title">
				Приветствую, <input type="text" id="name" placeholder="Sergey" v-model="name">
			</h2>
		</section>

		<section class="create-todo">
			<h3>Создать задачу</h3>

			<form id="new-todo-form" @submit.prevent="addTodo">
				<h4>Какую категорию?</h4>
				<input 
					type="text" 
					name="content" 
					id="content" 
					placeholder="Поспать 8 часов"
					v-model="input_content" />
				
				<h4>Выбрать категорию</h4>
				<div class="options">

					<label>
						<input 
							type="radio" 
							name="category" 
							id="category1" 
							value="Бизнес"
							v-model="input_category" />
						<span class="bubble business"></span>
						<div>Работа</div>
					</label>

					<label>
						<input 
							type="radio" 
							name="category" 
							id="category2" 
							value="Персонал"
							v-model="input_category" />
						<span class="bubble personal"></span>
						<div>Персонал</div>
					</label>

				</div>

				<input type="submit" value="Добавить задачу" />
			</form>
		</section>

		<section class="todo-list">
			<h3>Все задачи</h3>
			<div class="list" id="todo-list">

				<div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
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
						<button class="delete" @click="removeTodo(todo)">Удалить</button>
					</div>
				</div>

			</div>
		</section>

	</main>
</template>
