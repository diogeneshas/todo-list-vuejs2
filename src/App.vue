<template>
	<div id="app">
		<h1>Tarefas</h1>
		<tasks-progress :progress="progress"></tasks-progress>
		<new-task @taskAdded="addTask"></new-task>
		<task-grid :tasks="tasks" @taskDeleted="deleteTask" @taskStateChanged="toggleTaskState"></task-grid>
	</div>
</template>

<script>
import TaskGrid from '@/components/TaskGrid.vue'
import NewTask from '@/components/NewTask.vue'
import TasksProgress from '@/components/TasksProgress'

export default {
	components: {
		TaskGrid, NewTask, TasksProgress
	},
	data() {
		return {
			tasks: []
		}
	},
	methods: {
		addTask(task) {
			const sameName = t => t.name === task.name
			const reallyNew = this.tasks.filter(sameName).length == 0
			if(reallyNew) {
				this.tasks.push({
					name: task.name,
					pending: task.pending || true
				})
			}
		},
		deleteTask(i) {
			this.tasks.splice(i, 1)
		},
		toggleTaskState(i) {
			this.tasks[i].pending = !this.tasks[i].pending
		}
	},
	computed: {
		progress() {
			const total = this.tasks.length
			const done = this.tasks.filter(t => !t.pending).length
			return Math.round(done / total * 100) || 0
		}
	},
	watch: {
		tasks: {
			deep: true,
			handler() {
				localStorage.setItem('tasks', JSON.stringify(this.tasks))
			}
		}
	},
	created() {
		const json = localStorage.getItem('tasks')
		this.tasks = JSON.parse(json) || []
	}
}
</script>

<style>
	body {
		font-family: 'Lato', sans-serif;
		background: linear-gradient(to right, rgb(22, 34, 42), rgb(58, 96, 115));
		color: #FFF;
	}

	#app {
		display: flex;
		flex: 1;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		height: 100vh;
	}

	#app h1 {
		margin-bottom: 5px;
		font-weight: 300;
		font-size: 3rem;
	}
</style>
