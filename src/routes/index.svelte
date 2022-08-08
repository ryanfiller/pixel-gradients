<script>
	let hasWindow = typeof window !== 'undefined'

	let name = 'pixel-gradient'
	let rows = 5
	let columns = 5
	let foreground = '#00FF00' // lime
	let background = '#191970' // midnightblue

	$: (
		hasWindow && (
			document.documentElement.style.setProperty('--foreground', foreground),
			document.documentElement.style.setProperty('--background', background)
		)
	)
</script>

<header>
	<h1>make pixel art with css graidents!</h1>
</header>

<hr />

<main>
	{#if hasWindow && !!window.localStorage}
		<fieldset>
			<label for='name'>
				<span>name</span>
				<input type='text' id='name' bind:value={name} />
			</label>
			<label for='rows'>
				<span>rows</span>
				<input type='number' id='rows' bind:value={rows} />
			</label>
			<label for='columns'>
				<span>columns</span>
				<input type='number' id='columns' bind:value={columns} />
			</label>
			<label for='foreground'>
				<span>foreground</span>
				<input type='color' id='foreground' bind:value={foreground} />
			</label>
			<label for='background'>
				<span>background</span>
				<input type='color' id='background' bind:value={background} />
			</label>
		</fieldset>

		<section>
			<article>

			</article>
			<article>

			</article>
			<article>

			</article>
		</section>
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
		--foreground: '';
		--background: ''
	}

	:global(*) {
		box-sizing: border-box;
		color: var(--foreground);
		border-color: var(--foreground);
		background-color: var(--background);
	}

	:global(*):focus {
		outline: .15em dotted var(--foreground);
		outline-offset: .125em;
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

	fieldset {
		display: flex;
		flex-wrap: wrap;
		align-items: stretch;
		gap: 1rem;
		padding: 1rem;
		border: none;
	}

	label {
		flex: 1;
	}

	label span {
		display: block;
		font-size: 1em;
	}

	input {
		width: 100%;
		min-width: 10em;
		padding: .5em;
		border-style: solid;
	}

	input[type='color'] {
		padding: 0;
		height: 2.5em;
	}

	footer {
		display: flex;
		gap: 1rem;
		align-items: center;
	}
</style>
