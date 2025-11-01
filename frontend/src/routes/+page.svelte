<script lang="ts">
	import { onMount } from 'svelte';

	const API_URL = import.meta.env.VITE_API_URL || 'http://localhost:8000/api';

	interface Todo {
		id: number;
		title: string;
		completed: boolean;
		created_at: string;
		updated_at: string;
	}

	let todos = $state<Todo[]>([]);
	let newTodoTitle = $state('');
	let loading = $state(false);
	let error = $state('');

	async function fetchTodos() {
		loading = true;
		error = '';
		try {
			const response = await fetch(`${API_URL}/todos/`);
			if (!response.ok) throw new Error('Failed to fetch todos');
			todos = await response.json();
		} catch (e) {
			error = e instanceof Error ? e.message : 'An error occurred';
		} finally {
			loading = false;
		}
	}

	async function addTodo() {
		if (!newTodoTitle.trim()) return;

		loading = true;
		error = '';
		try {
			const response = await fetch(`${API_URL}/todos/`, {
				method: 'POST',
				headers: {
					'Content-Type': 'application/json',
				},
				body: JSON.stringify({ title: newTodoTitle, completed: false })
			});
			if (!response.ok) throw new Error('Failed to add todo');
			const todo = await response.json();
			todos = [todo, ...todos];
			newTodoTitle = '';
		} catch (e) {
			error = e instanceof Error ? e.message : 'An error occurred';
		} finally {
			loading = false;
		}
	}

	async function toggleTodo(todo: Todo) {
		loading = true;
		error = '';
		try {
			const response = await fetch(`${API_URL}/todos/${todo.id}/`, {
				method: 'PATCH',
				headers: {
					'Content-Type': 'application/json',
				},
				body: JSON.stringify({ completed: !todo.completed })
			});
			if (!response.ok) throw new Error('Failed to update todo');
			const updatedTodo = await response.json();
			todos = todos.map(t => t.id === todo.id ? updatedTodo : t);
		} catch (e) {
			error = e instanceof Error ? e.message : 'An error occurred';
		} finally {
			loading = false;
		}
	}

	async function deleteTodo(id: number) {
		loading = true;
		error = '';
		try {
			const response = await fetch(`${API_URL}/todos/${id}/`, {
				method: 'DELETE'
			});
			if (!response.ok) throw new Error('Failed to delete todo');
			todos = todos.filter(t => t.id !== id);
		} catch (e) {
			error = e instanceof Error ? e.message : 'An error occurred';
		} finally {
			loading = false;
		}
	}

	onMount(() => {
		fetchTodos();
	});
</script>

<div class="container">
	<h1>Todo App..</h1>
	<p class="subtitle">Django + Svelte 5</p>

	{#if error}
		<div class="error">{error}</div>
	{/if}

	<div class="input-container">
		<input
			type="text"
			bind:value={newTodoTitle}
			placeholder="What needs to be done?"
			onkeydown={(e) => e.key === 'Enter' && addTodo()}
			disabled={loading}
		/>
		<button onclick={addTodo} disabled={loading || !newTodoTitle.trim()}>
			Add
		</button>
	</div>

	<div class="todos">
		{#if loading && todos.length === 0}
			<p class="loading">Loading...</p>
		{:else if todos.length === 0}
			<p class="empty">No todos yet. Add one above!</p>
		{:else}
			{#each todos as todo (todo.id)}
				<div class="todo-item" class:completed={todo.completed}>
					<input
						type="checkbox"
						checked={todo.completed}
						onchange={() => toggleTodo(todo)}
						disabled={loading}
					/>
					<span class="todo-title">{todo.title}</span>
					<button class="delete-btn" onclick={() => deleteTodo(todo.id)} disabled={loading}>
						Delete
					</button>
				</div>
			{/each}
		{/if}
	</div>

	<div class="stats">
		{todos.filter(t => !t.completed).length} items left
	</div>
</div>

<style>
	.container {
		max-width: 600px;
		margin: 2rem auto;
		padding: 0 1rem;
		font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
	}

	h1 {
		text-align: center;
		font-size: 3rem;
		font-weight: 100;
		color: #b83f45;
		margin-bottom: 0.5rem;
	}

	.subtitle {
		text-align: center;
		color: #777;
		margin-bottom: 2rem;
	}

	.error {
		background-color: #fee;
		color: #c33;
		padding: 0.75rem;
		border-radius: 4px;
		margin-bottom: 1rem;
	}

	.input-container {
		display: flex;
		gap: 0.5rem;
		margin-bottom: 1.5rem;
	}

	input[type="text"] {
		flex: 1;
		padding: 0.75rem 1rem;
		font-size: 1rem;
		border: 1px solid #ddd;
		border-radius: 4px;
	}

	input[type="text"]:focus {
		outline: none;
		border-color: #b83f45;
	}

	button {
		padding: 0.75rem 1.5rem;
		font-size: 1rem;
		background-color: #b83f45;
		color: white;
		border: none;
		border-radius: 4px;
		cursor: pointer;
		transition: background-color 0.2s;
	}

	button:hover:not(:disabled) {
		background-color: #a03238;
	}

	button:disabled {
		opacity: 0.5;
		cursor: not-allowed;
	}

	.todos {
		background: white;
		border: 1px solid #ddd;
		border-radius: 4px;
		overflow: hidden;
	}

	.todo-item {
		display: flex;
		align-items: center;
		gap: 1rem;
		padding: 1rem;
		border-bottom: 1px solid #eee;
		transition: background-color 0.2s;
	}

	.todo-item:last-child {
		border-bottom: none;
	}

	.todo-item:hover {
		background-color: #fafafa;
	}

	.todo-item.completed .todo-title {
		text-decoration: line-through;
		color: #888;
	}

	input[type="checkbox"] {
		width: 1.25rem;
		height: 1.25rem;
		cursor: pointer;
	}

	.todo-title {
		flex: 1;
		font-size: 1rem;
	}

	.delete-btn {
		padding: 0.5rem 1rem;
		font-size: 0.875rem;
		background-color: #dc3545;
	}

	.delete-btn:hover:not(:disabled) {
		background-color: #c82333;
	}

	.loading,
	.empty {
		padding: 2rem;
		text-align: center;
		color: #888;
	}

	.stats {
		margin-top: 1rem;
		padding: 0.5rem;
		color: #777;
		font-size: 0.875rem;
	}
</style>
