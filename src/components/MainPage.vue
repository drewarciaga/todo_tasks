<template>
<v-container class="">
	<v-responsive class="align-center">
		<h1 class="text-h2 font-weight-bold">To Do Tasks</h1>

		<div class="py-14" />

		<v-card
			max-width="1000"
			class="mx-auto"
		>
			<v-container>
				<v-btn class="task-counter" rounded="xl">
					<template v-slot:append>
						<v-badge
							color="info"
							:content="store.state.tasks.length"
							inline
							class="counter-badge"
						>Tasks
						</v-badge>
					</template>
				</v-btn>

				<v-btn class="task-counter task-done-counter" rounded="xl">
					<template v-slot:append>
						<v-badge
							color="info"
							:content="task_done_counter"
							inline
							class="counter-badge"
						>Tasks Done
						</v-badge>
					</template>
				</v-btn>

				<v-btn class="tasks-delete-btn" color="red">
					<v-icon
						icon="mdi-delete"
					></v-icon>
					Tasks
				</v-btn>

				<v-btn class="tasks-delete-btn" color="red" @click="deleteAllTaskDone()">
					<v-icon
						icon="mdi-delete"
					></v-icon>
					Tasks Done
				</v-btn>

				<br><br>
				<div class="main-list">
				<TransitionGroup name="fade">
				
					<v-row dense v-for="(task, i) in store.state.tasks" :key="i">
						<v-col cols="12">
							<v-card
								color="#385F73"
								theme="dark"
								class="task-card"
								:class="(task.status.done ? 'task-done' : '')"
							>
								<v-card-title class="text-h5">
									<v-btn 
										icon="mdi-check"
										density="compact"
										color="success"
										@click="changeTaskStatus(i)"
										:class="(task.status.done ? 'disable-check-btn' : '')"
									>	
									</v-btn>
									
									{{ task.name }}

									<v-btn 
										icon="mdi-delete"
										density="compact"
										color="red"
										class="delete-btn"
										@click="deleteTask(i)"
									>	
									</v-btn>
								</v-card-title>
							</v-card>
						</v-col>
					</v-row>
				
				</TransitionGroup>
			</div>

				<v-spacer></v-spacer>
				<br>

				<v-text-field
					v-model="task_name"
					variant="underlined"
					label="New Task"
					append-inner-icon="mdi-plus"
					@click:append-inner="addTask()"
				></v-text-field>
			</v-container>

		</v-card>
	</v-responsive>
</v-container>
</template>

<script setup>
import { ref, reactive, onMounted } from 'vue'
import { createToast } from 'mosha-vue-toastify';
import { createStore } from 'vuex'

const task_name 		= ref('')
const task_id   		= ref(0)
const task_done_counter = ref(0)

const store = createStore({
	state: {
		tasks: [{ id: task_id.value, name:'First Task', status: { done: false } }]
	},
	mutations: {
		addTask(state, { id, name }){
			state.tasks.push({
				id: id,
				name: name,
				status: { done: false }
			})
			task_name.value = ''
			task_id.value = id
		},
		deleteTask(state, { key }){
			state.tasks.splice(key, 1)
		},
		changeTaskStatus(state, { key }){
			state.tasks[key].status.done = true
		},
		deleteAllTaskDone(state){
			state.tasks.forEach(function(task, index, object) {
				if (task.status.done === true) {
					object.splice(index, 1);
				}
			});
		},
	}
})

const toast = (message,type, timer = 2000) => {
	createToast(message, {
		type: type,
		position: 'top-right',
		timeout: timer,
		hideProgressBar: true,
		showIcon: true,
	})
}

function addTask(){
	let name = task_name.value
	if(name == null || name == ''){
		toast(' Task Name cannot be empty!', 'danger', 2000 )
	}else{
		task_id.value++
		let id = task_id.value
		store.commit('addTask', { id, name })
		console.log(store.state)
	}
}
function deleteTask(key){
	store.commit('deleteTask', { key })
}

function changeTaskStatus(key){
	store.commit('changeTaskStatus', { key })
}

function deleteAllTaskDone(){
	store.commit('deleteAllTaskDone')
}
</script>

<style>
.mdi-plus{
	color: green;
	background-color:aliceblue;
}
.fade-enter-active, .fade-leave-active {
	transition: opacity 0.5s ease;
}

.fade-enter-from, .fade-leave-to {
	opacity: 0;
}
.counter-badge .v-badge__badge{
	margin-left: 4px;
}
.task-counter{
	margin-bottom: 5px;
	margin-right: 5px;
}
.main-list{
	max-height: 300px;
	overflow-y: auto;
	overflow-x: hidden;
}

.delete-btn{
	float: right;
}
.tasks-delete-btn{
	float: right;
	margin-right: 5px;
}
.task-card :hover{
	opacity: .5;
}
.task-done, .disable-check-btn.bg-success{
	background-color: gray !important;
}
</style>