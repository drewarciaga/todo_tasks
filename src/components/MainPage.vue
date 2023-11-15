<template>
<v-container class="">
	<v-responsive class="align-center">
		<h1 class="text-h2 font-weight-bold">To Do Tasks</h1><p class="pl-2">by Andrew Arciaga</p>

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
							:content="store.getters.tasksCount"
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
							:content="store.getters.tasksDoneCount"
							inline
							class="counter-badge"
						>Tasks Done
						</v-badge>
					</template>
				</v-btn>

				<v-btn class="tasks-delete-btn" color="red" @click="store.dispatch('deleteAllTasks')">
					<v-icon
						icon="mdi-delete"
					></v-icon>
					Tasks
				</v-btn>

				<v-btn class="tasks-delete-btn" color="red" @click="store.dispatch('deleteAllTaskDone')"  v-show="store.getters.tasksDoneCount > 0">
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
										@click="store.dispatch('changeTaskStatus', i)"
										:class="(task.status.done ? 'disable-check-btn' : '')"
									>	
									</v-btn>
									
									{{ task.name }}

									<v-btn 
										icon="mdi-delete"
										density="compact"
										color="red"
										class="delete-btn"
										@click="store.dispatch('deleteTask', i)"
									>	
									</v-btn>
								</v-card-title>
							</v-card>
						</v-col>
					</v-row>
				
				</TransitionGroup>
			</div>
				<br>
				<v-text-field
					v-model="task_name"
					variant="underlined"
					label="New Task"
					append-inner-icon="mdi-plus"
					@click:append-inner="store.dispatch('addTask')"
				></v-text-field>
			</v-container>

		</v-card>
	</v-responsive>
</v-container>
</template>

<script setup>
import { ref } from 'vue'
import { createStore } from 'vuex'
import useToast from '../Composables/useToast.js'

const task_name 		= ref('')
const task_id   		= ref(0)
const { toast } 		= useToast()

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
		deleteTask(state, key){
			state.tasks.splice(key, 1)
		},
		changeTaskStatus(state, { key }){
			state.tasks[key].status.done = true
		},
		deleteAllTaskDone(state){
			let orig_length  = state.tasks.length

			state.tasks = state.tasks.filter(function(obj) { 
				return obj.status.done === false;
			})

			if(state.tasks.length < orig_length){
				toast(' Tasks Done Deleted Successfully', 'success', 2000 )
			}else{
				toast(' No Tasks Done found!', 'danger', 2000 )
			}
		},
		deleteAllTasks(state){
			if(state.tasks.length == 0){
				toast(' No Tasks found!', 'danger', 2000 )
			}else{
				state.tasks = []
				toast(' All Tasks Deleted Successfully', 'success', 2000 )
			}

		},
	},
	actions: {
		addTask({ commit }) {
			let name = task_name.value
			if(name == null || name == ''){
				toast(' Task Name cannot be empty!', 'danger', 2000 )
			}else{
				task_id.value++
				let id = task_id.value
				commit('addTask', { id, name })
				toast(' Task Added Successfully!', 'success', 2000 )
				console.log(store.state)
			}
		},
		deleteTask({ commit }, key) {
			commit('deleteTask', key)
			toast(' Task Deleted Successfully!', 'success', 2000 )
		},
		changeTaskStatus({ commit }, key) {
			commit('changeTaskStatus', { key })
		},
		deleteAllTaskDone({ commit }) {
			commit('deleteAllTaskDone')
		},
		deleteAllTasks({ commit }) {
			commit('deleteAllTasks')
		},
	},
	getters: {
		tasksCount(state) {
			return state.tasks.length
		},
		tasksDoneCount(state) {
			return state.tasks.filter(function(obj) { 
				return obj.status.done === true;
			}).length
		}
	}
})

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