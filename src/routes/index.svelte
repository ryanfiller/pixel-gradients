<script>
	let hasWindow = typeof window !== 'undefined'

	let name = 'pixel-gradient'
	let pixelSize = '0.25rem'
	let foreground = '#00FF00' // lime
	let background = '#191970' // midnightblue

	let rows = 5
	let columns = 5

	$: selected = [
		[true,  false, false, false, true ],
		[false, true,  false, true,  false],
		[false, false, true,  false, false],
		[false, true,  false, true,  false],
		[true,  false, false, false, true ]
	]

	$: shownGrid = Array(rows).fill(Array(columns).fill(false))
	// keep the checkbox from exploding when rows index is undfined
	$: if (rows > selected.length) {
		const difference = rows - selected.length
		selected.push(Array(difference).fill(false))
	}

	/* <image>  <position> / <size> */
	/* graident x y       / height width */
	$: CSS = shownGrid.map((row, rIndex) => {
		return row.map((_, cIndex) => {
			if (selected[rIndex][cIndex]) {
				return `no-repeat linear-gradient(var(--foreground), var(--foreground)) calc(${rIndex} * var(--pixelSize)) calc(${cIndex} * var(--pixelSize)) / var(--pixelSize) var(--pixelSize)`
			} else {
				return 'SKIP'
			}
		})
	})
		.flat() // make this one array, [], instead of a nested one, [[], [], ...]
		.filter(value => !(value === 'SKIP')) // get rid of the blank declarations
		.join(', \n \t') //make it look like CSS

	// sync up with the CSS
	$: hasWindow && (
		document.documentElement.style.setProperty('--foreground', foreground),
		document.documentElement.style.setProperty('--background', background),
		document.documentElement.style.setProperty('--pixelSize', pixelSize),
		document.documentElement.style.setProperty(`--${name}`, CSS)
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
				<span>name: </span>
				<input type='text' id='name' bind:value={name} />
			</label>
			<label for='rows'>
				<span>rows: </span>
				<input type='number' min='0' id='rows' bind:value={rows} />
			</label>
			<label for='columns'>
				<span>columns: </span>
				<input type='number' min='0' id='columns' bind:value={columns} />
			</label>
			<label for='foreground'>
				<span>foreground: </span>
				<input type='color' id='foreground' bind:value={foreground} />
			</label>
			<label for='background'>
				<span>background: </span>
				<input type='color' id='background' bind:value={background} />
			</label>
			<label for='pixelsize'>
				<span>pixel size: </span>
				<input type='text' id='pixelsize' bind:value={pixelSize} />
			</label>
		</fieldset>

		<section class='checkboxes'>
			<table>
				{#each shownGrid as row, rIndex}
					<tr>
						{#each row as _column, cIndex}
							<td>
								<input
									type='checkbox'
									id={`${rIndex}-${cIndex}`}
									bind:checked={selected[rIndex][cIndex]}
									>
								<label
									for={`${rIndex}-${cIndex}`}
								/>
							</td>
						{/each}
					</tr>
				{/each}
			</table>
		</section>

		<section class='output'>
			<div style={`background: var(--${name}); width: calc(${columns} * var(--pixelSize)); height: calc(${rows} * var(--pixelSize));`}/>
		</section>

	<!-- formatting tabs in code with html is hard... -->
		<section class='code'>
			<pre><code>--{name}:
	{CSS}
;</code></pre>
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
		margin: 1rem 0;
	}

	strong {
		display: inline;
	}

	main {
		flex: 1;
		gap: 1rem;
		display: flex;
		flex-wrap: wrap;
	}

	fieldset {
		display: flex;
		flex-wrap: wrap;
		align-items: stretch;
		gap: 1rem;
		padding: 1rem;
		border: none;
		width: 100%;
	}

	label {
		flex: 1;
		cursor: pointer;
	}

	label span {
		display: block;
		font-size: 1em;
	}

	input {
		width: 100%;
		cursor: pointer;
		min-width: 10em;
		padding: .5em;
		border-style: solid;
	}

	input[type='color'] {
		padding: 0;
		height: 2.5em;
	}

	section {
		border: 1px solid var(--foreground);
	}

	section.checkboxes,
	section.output {
		flex: 1;
		min-width: 20rem;
	}

	table {
		width: 100%;
		border-collapse: collapse;
		cellspacing: 0;
		cellpadding: 0;
	}

	tr, td {
		border: 1px solid var(--foreground);
	}

	table input[type='checkbox'] {
		visibility: hidden;
		position: absolute;
		pointer-events: none;
	}

	table label {
		display: block;
		background: var(--background);
		aspect-ratio: 1 / 1;
	}

	table input[type='checkbox']:checked + label {
		background: var(--foreground);
	}

	section.output {
		display: flex;
		align-items: center;
		justify-content: center;
	}

	section.code {
		flex: 1;
		min-width: 100%;
	}

	pre {
		overflow: auto;
		padding: 1rem;
	}

	footer {
		display: flex;
		gap: 1rem;
		align-items: center;
	}
</style>
