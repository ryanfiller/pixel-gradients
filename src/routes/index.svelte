<script>
	let hasWindow = typeof window !== 'undefined'

	function tryLocalStorage(key = '') {
		const needsParsing = ['pixelSize', 'rows', 'columns', 'selected']
		if (hasWindow && window.localStorage.getItem(key)) {
			if (needsParsing.includes(key)) {
				return JSON.parse(window.localStorage.getItem(key))
			} else {
				return window.localStorage.getItem(key)
			}
		}
	}

	let name = tryLocalStorage('name') || 'pixel-gradient'
	let pixelSize = tryLocalStorage('pixelSize') || 0.25
	let foreground = tryLocalStorage('foreground') || '#00FF00' // lime
	let background = tryLocalStorage('background') || '#191970' // midnightblue

	let rows = tryLocalStorage('rows') || 5
	let columns = tryLocalStorage('columns') || 5

	const x = [
		[true,  false, false, false, true ],
		[false, true,  false, true,  false],
		[false, false, true,  false, false],
		[false, true,  false, true,  false],
		[true,  false, false, false, true ]
	]

	$: selected = tryLocalStorage('selected') || x

	// keep the checkbox from exploding when rows index is undfined
	$: if (rows > selected.length) {
		const rowDifference = rows - selected.length
		// must reassign for Svelte to see this, can't just .push to selected
		selected = [...selected, ...Array(rowDifference).fill(Array(columns))]
	}

	$: grid = Array(rows).fill(Array(columns).fill(false))

	/* <image>  <position> / <size> */
	/* graident x y       / height width */
	$: CSS = grid.map((row, rIndex) => {
		return row.map((_, cIndex) => {
			if (selected[rIndex][cIndex]) {
				return `no-repeat linear-gradient(var(--foreground), var(--foreground)) calc(${cIndex} * var(--pixelSize)) calc(${rIndex} * var(--pixelSize)) / var(--pixelSize) var(--pixelSize)`
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
		document.documentElement.style.setProperty('--pixelSize', `${pixelSize}rem`),
		document.documentElement.style.setProperty(`--${name}`, CSS)
	)

	// sync up with localStorage
	$: hasWindow && (
		window.localStorage.setItem('name', name),
		window.localStorage.setItem('pixelSize', JSON.stringify(pixelSize)),
		window.localStorage.setItem('foreground', foreground),
		window.localStorage.setItem('background', background),
		window.localStorage.setItem('rows', JSON.stringify(rows)),
		window.localStorage.setItem('columns', JSON.stringify(columns)),
		window.localStorage.setItem('selected', JSON.stringify(selected))
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
				<span>pixel size: {pixelSize}rem</span>
				<input type='range' min='0.25' max='5' step='0.25' id='pixelsize' bind:value={pixelSize} />
			</label>
		</fieldset>

		<section class='checkboxes'>
			<table>
				{#each grid as row, rIndex}
					<tr>
						{#each row as _column, cIndex}
							<td>
								<input
									type='checkbox'
									id={`${rIndex}-${cIndex}`}
									bind:checked={selected[rIndex][cIndex]}
								/>
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
		width: 100%;
		border-radius: 0;
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
		height: 0;
		width: 0;
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
		padding: 1rem;
		min-height: 25vh;
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
