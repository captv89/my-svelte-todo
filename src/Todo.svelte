<script>
	import { onMount } from 'svelte';
	import axios from 'axios';
	import Icon from 'svelte-icons-pack/Icon.svelte';
	import AiOutlineDelete from 'svelte-icons-pack/ai/AiOutlineDelete';

	let isError = null;
	let todos = [];
	let todoItem = '';
	let todoStatus = false;

	onMount(async () => {
		try {
			const response = await axios.get('http://localhost:1337/api/todos');
			todos = response.data.data;
			console.log(todos);
		} catch (error) {
			isError = error;
		}
	});

	// function to handle add todo
	const handleAddTodo = async () => {
		try {
			if (!todoItem) return alert('please add a goal for today!');
			const jsonBody = JSON.stringify({
				data: {
					item: todoItem,
					isCompleted: todoStatus,
				},
			});
			console.log(jsonBody);
			const response = await axios.post(
				'http://localhost:1337/api/todos',
				jsonBody,
				{
					headers: {
						'Content-Type': 'application/json',
					},
				}
			);
			console.log(response);
			todos = [...todos, response.data.data];
			console.log(todos);
			todoItem = '';
		} catch (error) {
			isError = error;
			console.log(error);
		}
	};

	// function to handle delete todo
	const handleDeleteTodo = async (id) => {
		try {
			const response = await axios.delete(
				`http://localhost:1337/api/todos/${id}`
			);
			console.log(response);
			todos = todos.filter((todo) => todo.id !== id);
		} catch (error) {
			isError = error;
			console.log(error);
		}
	};

	// function to handle update todo
	const handleUpdateTodo = async (id, change, status) => {
		try {
			const jsonBody = JSON.stringify({
				data: {
					item: change,
					isCompleted: status,
				},
			});
			const response = await axios.put(
				`http://localhost:1337/api/todos/${id}`,
				jsonBody,
				{
					headers: {
						'Content-Type': 'application/json',
					},
				}
			);
			console.log(response);
		} catch (error) {
			isError = error;
			console.log(error);
		}
	};

	// function to handle update todo
	const handleStatusTodo = async (id, change, status) => {
		try {
			const jsonBody = JSON.stringify({
				data: {
					item: change,
					isCompleted: !status,
				},
			});
			const response = await axios.put(
				`http://localhost:1337/api/todos/${id}`,
				jsonBody,
				{
					headers: {
						'Content-Type': 'application/json',
					},
				}
			);
			console.log(response);
		} catch (error) {
			isError = error;
			console.log(error);
		}
	};

	// on keypress enter
	const handleKeyPress = (e) => {
		if (e.key === 'Enter') {
			handleAddTodo();
		}
	};
</script>

<div class="flex-col max-w-4xl m-auto place-content-center">
	{#if todos.length > 0}
		<p class="text-2xl mb-4 text-center font-mono">Today's Goal</p>
	{/if}
	{#if isError}
		<p class="text-xl mb-2 text-red-600">{isError}</p>
	{:else}
		<!-- Div to capture input todo -->
		<div class="flex bg-white justify-between p-1 items-center mb-4 ">
			<input
				type="text"
				class="border-1 border-gray-300 p-2 m-1 rounded-md w-5/6"
				bind:value={todoItem}
				placeholder="Add a goal for today"
				on:keypress={handleKeyPress}
			/>
			<button
				class="bg-blue-500 text-white p-2 place-items-center rounded-md hover:bg-blue-600 w-1/6 transition duration-300 ease-in-out"
				on:click={handleAddTodo}
			>
				+
			</button>
		</div>
		<ul>
			{#each todos.filter((todo) => todo.attributes.isCompleted === false) as todo}
				<li
					class="rounded-xl bg-black bg-opacity-10  p-5 mb-4 flex items-center justify-between cursor-pointer hover:shadow-lg transition transform hover:scale-10"
				>
					<div class="flex items-center w-full">
						<input
							type="checkbox"
							class="mr-3"
							bind:checked={todo.attributes.isCompleted}
							on:click={handleStatusTodo(
								todo.id,
								todo.attributes.item,
								todo.attributes.isCompleted
							)}
						/>
						<input
							class:completed={todo.attributes.isCompleted}
							class="border-0 bg-transparent overflow-wrap: normal whitespace-normal  w-full"
							bind:value={todo.attributes.item}
							on:change={handleUpdateTodo(
								todo.id,
								todo.attributes.item,
								todo.attributes.isCompleted
							)}
						/>
					</div>
					<button
						on:click={handleDeleteTodo(todo.id)}
						class="border-0"><Icon src={AiOutlineDelete} /></button
					>
				</li>
			{/each}
		</ul>

		<!-- Done Items -->
		{#if todos.filter((todo) => todo.attributes.isCompleted === true).length > 0}
			<p class="text-2xl mb-4 text-center font-mono">Done</p>
		{/if}
		<ul>
			{#each todos.filter((todo) => todo.attributes.isCompleted === true) as todo}
				<li
					class="flex  rounded-xl bg-black bg-opacity-5 border-1 p-5 mb-4 items-center justify-between cursor-pointer hover:shadow-lg transition transform hover:scale-10"
				>
					<div class="flex items-center w-full">
						<input
							type="checkbox"
							class="mr-3"
							bind:checked={todo.attributes.isCompleted}
							on:click={handleStatusTodo(
								todo.id,
								todo.attributes.item,
								todo.attributes.isCompleted
							)}
						/>
						<input
							disabled="true"
							class:completed={todo.attributes.isCompleted}
							class="border-0 text-decoration-color: #fff overflow-wrap: normal whitespace-normal  text-decoration-line: line-through w-full"
							bind:value={todo.attributes.item}
						/>
					</div>
					<button
						on:click={handleDeleteTodo(todo.id)}
						class="border-0"><Icon src={AiOutlineDelete} /></button
					>
				</li>
			{/each}
		</ul>
	{/if}
	<div class="m-3" />
</div>
