<template>
	<div>
	  <div class="content">
		 <div class="block">
			<div class="header">
			  <h1>ToDo List</h1>
			  <p>Get things done, one item at a time.</p>
			  <div class="line"></div>
			</div>
			<div class="body">
			  <ul v-if="!loading">
				 <li v-for="(task, index) in sortedTasks" :key="index">
					<div v-if="!task.editing">
						<p class="as" :class="{ 'completed-task': task.completed }">{{ task.title }}</p>
					</div>
					<div v-else>
						<input v-model="task.newTitle" type="text" class="text-black">
						<button @click="saveEdit(task)">Save</button>
					</div>
					<div class="icons">
					  <input @click="toggleCompleted(task)" type="checkbox" :checked="task.completed">
					  <Icon @click="deleteUser(task.id)" name="lucide:delete"/>
					  <Icon @click="changeItem(task)" name="lucide:pen"/>
					</div>
					<div v-if="task.completed" class="line-finished"></div>
					<p v-if="task.deleted" class="deleted-text">Deleted</p>
				 </li>
			  </ul>
			  <div v-else>
				  <p>Loading tasks...</p>
			  </div>
			  <div class="check">
				 <h1>Move done items at the end?</h1>
				 <input type="radio" v-model="moveCompletedToEnd">
			  </div>
			</div>
			<div class="footer">
			  <h1>Add to the todo list</h1>
			  <div class="add">
				 <input v-model="titleInput" placeholder="Add task" type="text">
				 <button @click="addUser">Add</button>
				 <p v-if="completed" class="my-3 text-green-200">The task added</p>
			  </div>
			</div>
		 </div>
	  </div>
	</div>
 </template>
 
 <script setup>
 import axios from 'axios'
 import { ref, computed, onMounted } from 'vue'
 
 const user = ref([])
 const titleInput = ref('')
 const completed = ref(false)
 const moveCompletedToEnd = ref(false)
 const loading = ref(true) 
 
 const getUsers = async () => {
	try {
	  const response = await axios.get('https://jsonplaceholder.typicode.com/todos')
	  user.value = response.data.map(task => ({ ...task, deleted: false, editing: false, newTitle: task.title }))
	} catch (err) {
	  console.log(err)
	} finally {
	  loading.value = false
	}
 }
 
 const addUser = async () => {
	try {
	  const response = await axios.post('https://jsonplaceholder.typicode.com/todos', { title: titleInput.value, completed: false })
	  user.value.push({ ...response.data, deleted: false, editing: false, newTitle: titleInput.value })
	  titleInput.value = ''
	  completed.value = true
	  setTimeout(() => completed.value = false, 2000)
	} catch (err) {
	  console.log(err)
	}
 }
 
 const deleteUser = async (id) => {
	try {
	  await axios.delete(`https://jsonplaceholder.typicode.com/todos/${id}`)
	  const task = user.value.find(task => task.id === id)
	  if (task) task.deleted = true
	  setTimeout(() => task.deleted = false, 2000)
	} catch (err) {
	  console.log(err)
	}
 }
 
 const toggleCompleted = (task) => {
	task.completed = !task.completed
 }
 
 const changeItem = (task) => {
	task.editing = true
 }
 
 const saveEdit = async (task) => {
	try {
	  if (response.status === 200) {
		task.title = task.newTitle
		task.editing = false
	  }
	} catch (err) {
	  console.log(err)
	}
 }
 
 const sortedTasks = computed(() => {
	return moveCompletedToEnd.value
	  ? [...user.value.filter(task => !task.completed), ...user.value.filter(task => task.completed)]
	  : user.value
 })
 
 onMounted(getUsers)
 </script>
 
 <style scoped>
 .content {
	background-color: red;
	opacity: 0.7;
	width: 100%;
	min-height: 100vh;
	display: flex;
	align-items: center;
	justify-content: center;
 }
 .block {
	border: 2px solid white;
	color: white;
	padding: 90px 80px;
 }
 .header h1 {
	font-size: 40px;
 }
 .header p {
	padding: 10px 0;
 }
 .as {
	position: relative;
 }
 .header .line {
	background-color: white;
	width: 100%;
	height: 1px;
	margin: 10px 0;
 }
 .body ul li {
	background-color: grey;
	padding: 10px 10px;
	font-size: 20px;
	display: flex;
	align-items: center;
	justify-content: space-between;
	margin: 5px 0;
 }
 .icons {
	display: flex;
	gap: 5px;
 }
 .check {
	display: flex;
	align-items: center;
	justify-content: end;
	gap: 10px;
	margin: 10px 0;
 }
 .footer {
	margin-top: 30px;
 }
 .footer h1 {
	font-size: 20px;
 }
 .footer input {
	padding: 10px 30px;
	color: black;
 }
 .add {
	margin-top: 10px;
 }
 .footer button {
	background-color: transparent;
	border: 1px solid white;
	padding: 9px 30px;
 }
 .line-finished {
	top: 25px;
	left: 0;
	background-color: white;
	width: 100%;
	height: 1px;
	position: absolute;
 }
 .deleted-text {
	color: red;
	font-size: 12px;
 }
 .completed-task {
	text-decoration: line-through;
 }
 </style>
