<script>
	let hasWindow = typeof window !== 'undefined'
</script>

<header>
	make pixel art with css graidents
</header>

<hr />

<main>
	{#if hasWindow && !!window.localStorage}
		// TODO
	{/if}
</main>

<footer>
	<hr />
	<a href="https://github.com/ryanfiller/pixel-gradients">
		<strong>code</strong>
	</a>
	|
	<a href="https://github.com/ryanfiller/pixel-gradients/issues">
		<strong>bugs?</strong>
	</a>
	|
	<a href="https://www.ryanfiller.com?link=pixel-gradients">
		<strong>ryanfiller.com</strong>
	</a>
</footer>

<style>
	:root {
		--readable: 50rem;
	}

	:global(html),
	:global(body) {
		display: flex;
		flex-direction: column;
		gap: 1rem;
		padding: 0.5rem;
		height: calc(100vh - 1rem);
	}

	header {
		display: grid;
		gap: 0 1rem;
		grid-template-columns: auto minmax(auto, var(--readable)) auto;
	}

	header > * {
		grid-column: 2 / 3;
	}

	hr {
		width: 100%;
	}

	strong {
		display: inline;
	}

	main {
		flex: 1;
		display: flex;
		flex-direction: column;
	}

	footer {
		display: flex;
		gap: 1rem;
		align-items: center;
	}
</style>
