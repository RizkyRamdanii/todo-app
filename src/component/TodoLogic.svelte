<script lang="ts">
	type Todo = {
		text: string;
		done: boolean;
	};
	type Filters = 'all' | 'active' | 'completed';

	let todos = $state<Todo[]>([]);
	let filter = $state<Filters>('all');
	let filteredTodos = $derived(filterTodos());

	$effect(() => {
		const savedTodos = localStorage.getItem('todos');
		savedTodos && (todos = JSON.parse(savedTodos));
	});

	$effect(() => {
		localStorage.setItem('todos', JSON.stringify(todos));
	});

	function addTodo(event: KeyboardEvent) {
		if (event.key !== 'Enter') return;

		const todoEl = event.target as HTMLInputElement;
		const text = todoEl.value;
		const done = false;

		todos = [...todos, { text, done }];

		todoEl.value = '';
	}

	function editTodo(event: Event) {
		const inputEl = event.target as HTMLInputElement;
		const index = inputEl.dataset.index;
		todos[index].text = inputEl.value;
	}

	function toggleTodo(event: Event) {
		const inputEl = event.target as HTMLInputElement;
		const index = +inputEl.dataset.index!;
		todos[index].done = !todos[index].done;
	}

	function setFilter(newFilter: Filters) {
		filter = newFilter;
	}

	function filterTodos() {
		switch (filter) {
			case 'all':
				return todos;
			case 'active':
				return todos.filter((todo) => !todo.done);
			case 'completed':
				return todos.filter((todo) => todo.done);
		}
	}

	function remaining() {
		return todos.filter((todo) => !todo.done).length;
	}
</script>

<div class="flex flex-col items-center justify-center gap-6">
	<input class="p-4 rounded" onkeydown={addTodo} type="text" placeholder="Add Todo" />
	<div class="grid">
		{#each filteredTodos as todo, i}
			<div class:completed={todo.done} class="todo relative">
				<input
					class="p-4 rounded"
					oninput={editTodo}
					data-index={i}
					value={todo.text}
					type="text"
				/>
				<input
					class="absolute"
					onchange={toggleTodo}
					data-index={i}
					checked={todo.done}
					type="checkbox"
				/>
			</div>
		{/each}
	</div>
</div>
<div class="filters">
	<button onclick={() => setFilter('all')}>All</button>
	<button onclick={() => setFilter('active')}>Active</button>
	<button onclick={() => setFilter('completed')}>Completed</button>
</div>

<p>{remaining()} remaining</p>

<style>
	input[type='text'] {
		background: transparent;
	}

	input[type='checkbox'] {
		right: 4%;
		top: 50%;
		translate: 0% -50%;
	}

	button {
		background-color: #475569;
		padding: 13px 24px;
		border-radius: 0.35rem;
		transition: 0.2s;
		font-weight: 400;
	}

	button:hover {
		background-color: #1e293b;
	}

	.filters {
		margin-block: 2rem;
	}

	.todo {
		transition: opacity 0.3s;
	}

	.completed {
		opacity: 0.4;
		text-decoration: line-through;
	}
</style>
