<script lang="ts">
	import '../app.css';
	let { children } = $props();
</script>

<svelte:head>
	<title>Jackson Mathis</title>
	<meta name="description" content="Jackson Mathis — developer, thinker, builder." />
</svelte:head>

<div class="scanlines min-h-screen">
	{@render children()}
</div>

{@render children()}
