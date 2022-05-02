<template>
	<div>
		<header>
			<h1>Vue Todo with Typescript</h1>
		</header>
		<main>
			<TodoInput :item="todoText" @input="updateTodoText" @add="addTodoItem" />
			<div>
				<ul>
					<TodoListItem
						v-for="(todoItem, index) in todoItems"
						:key="index"
						:index="index"
						:todoItem="todoItem"
						@remove="removeTodoItem"
						@toggle="toggleTodoItemComplete"
					></TodoListItem>
				</ul>
			</div>
		</main>
	</div>
</template>

<script lang="ts">
import Vue from 'vue';
import TodoInput from './components/TodoInput.vue';
import TodoListItem from './components/TodoListItem.vue';

const STORAGE_KEY = 'vue-todo-ts-v1';

const storage = {
	save(todoItems: Todo[]) {
		const parsed = JSON.stringify(todoItems);
		localStorage.setItem(STORAGE_KEY, parsed);
	},
	fetch(): Todo[] {
		const todoItems = localStorage.getItem(STORAGE_KEY) || '[]';
		const result = JSON.parse(todoItems);
		return result;
	},
};

export interface Todo {
	title: string;
	done: boolean;
}

export default Vue.extend({
	components: { TodoInput, TodoListItem },
	data() {
		return {
			todoText: '',
			todoItems: [] as Todo[],
		};
	},
	methods: {
		updateTodoText(value: string) {
			this.todoText = value;
		},
		addTodoItem() {
			const value = this.todoText;

			if (value === '') {
				return alert('입력 내용이 없습니다.');
			}

			const todo: Todo = { title: value, done: false };
			this.todoItems.push(todo);
			storage.save(this.todoItems);
			this.initTodoIext();
		},
		initTodoIext() {
			this.todoText = '';
		},
		fetchTodoItems() {
			this.todoItems = storage.fetch().sort((a, b) => {
				if (a.title < b.title) {
					return -1;
				}
				if (a.title > b.title) {
					return 1;
				}
				return 0;
			});
		},
		removeTodoItem(index: number) {
			this.todoItems.splice(index, 1);
			storage.save(this.todoItems);
		},
		toggleTodoItemComplete(todoItem: Todo, index: number) {
			this.todoItems.splice(index, 1, {
				...todoItem,
				done: !todoItem.done,
			});
			storage.save(this.todoItems);
		},
	},
	created() {
		this.fetchTodoItems();
	},
});
</script>

<style scoped></style>
