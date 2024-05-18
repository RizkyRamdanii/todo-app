<script>
	import '../app.css';
</script>

<svelte:head>
	<title>Todo App</title>
</svelte:head>

<slot />

<style>
	:global(html, body) {
		height: 100%;
		font-family: 'Poppins', sans-serif;
	}

	:global(body) {
		place-content: center;
		display: grid;
		color: hsl(220 10% 98%);
		background-color: #0f172a;
	}
</style>
