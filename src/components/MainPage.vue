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
				<v-row dense v-for="(task, i) in tasks" :key="i">
					<v-col cols="12">
						<v-card
							color="#385F73"
							theme="dark"
						>
							<v-card-title class="text-h5">
								<v-btn variant="text">
									Listen Now
								</v-btn>
								
								{{ task.name }}
							</v-card-title>
						</v-card>
					</v-col>
				</v-row>

				<v-spacer></v-spacer>

				<v-text-field>
				<template v-slot:append>
					<v-btn variant="text">
						<v-icon color="green">
							mdi-plus
						</v-icon>
					</v-btn>
				</template>
		
				</v-text-field>

				<v-text-field
            variant="underlined"
            label="New Task"
            append-inner-icon="mdi-plus"
          ></v-text-field>
			</v-container>

		</v-card>
	</v-responsive>
</v-container>
</template>

<script setup>
import { ref, reactive, onMounted } from 'vue'
const tasks = ref([])
const task = reactive({
		id: '',
		name: '',
		status: { done: false }
})
onMounted(async () => {

	tasks.value.push( { name: 'test' })
	tasks.value.push( { name: 'test2' })
})
</script>

<style>
.mdi-plus{
	color: green;
	background-color:aliceblue;
}
</style>