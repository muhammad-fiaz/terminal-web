<script lang="ts">
	import '../app.css';

	let { children } = $props();
</script>
<svelte:head>
	<title>Terminal Portfolio</title>
	<meta name="description" content="A command-line style portfolio built with SvelteKit by Muhammad Fiaz." />
</svelte:head>
{@render children()}
