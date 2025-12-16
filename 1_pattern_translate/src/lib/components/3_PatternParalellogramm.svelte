<!-- 
  - HTML und Svelte Code zeigen + Funktionsaufrufe
  - Zeige wie das erste kleine Pattern beginnt (außerhalb der Schleife)

  - 2 Rechtecke in der Schleife und entsprechend der Indizes x, y, width und height anpassen
  - Funktionen müssen sie schreiben 
  - Kleiner Tipp mit data-x um zu sehen welche Indizes die Rects haben
-->

<script>
	const squareCount = 20;
	const squareSize = 1000 / squareCount;
	const offset = squareSize / 4; // 45-Grad-Versatz

	let factor = 1;
	let scaleFactor = 1;
	let heightFactor = 1;
	let rotationAngle = 0;

	// function calculateSizeCords1(xi, yi) {

	// }

	// function calculateSizeCords2(xi, yi) {

	// }
</script>

<div id="control">
	<div class="control-item">
		<label>Abstand:</label>
		<div class="input-group">
			<input type="range" min="1" max="2.5" step="0.05" bind:value={factor} />
			<input type="number" min="1" max="2.5" step="0.05" bind:value={factor} />
		</div>
	</div>
	<div class="control-item">
		<label>Breite:</label>
		<div class="input-group">
			<input type="range" min="0.5" max="2" step="0.05" bind:value={scaleFactor} />
			<input type="number" min="0.5" max="2" step="0.05" bind:value={scaleFactor} />
		</div>
	</div>
	<div class="control-item">
		<label>Höhe:</label>
		<div class="input-group">
			<input type="range" min="0.5" max="2" step="0.05" bind:value={heightFactor} />
			<input type="number" min="0.5" max="2" step="0.05" bind:value={heightFactor} />
		</div>
	</div>
	<div class="control-item">
		<label>Rotation:</label>
		<div class="input-group">
			<input type="range" min="0" max="360" step="1" bind:value={rotationAngle} />
			<input type="number" min="0" max="360" step="1" bind:value={rotationAngle} />
		</div>
	</div>
</div>

<div class="svg-container">
	<svg viewBox="0 0 1000 1000" class="svg-canvas">
		{#each Array(10) as _, j}
			{#each Array(10) as _, i}
				<g transform="translate({i * 200 * factor} {j * 100 * factor})">
					<polygon transform="rotate({rotationAngle}) scale({scaleFactor}, {heightFactor})" points="0 0 100 0 150 50 50 50" fill="Lightsteelblue" />
					<polygon transform="rotate({rotationAngle}) scale({scaleFactor}, {heightFactor})" points="50 50 150 50 200 100 100 100" fill="Steelblue" />
					<polygon transform="rotate({rotationAngle}) scale({scaleFactor}, {heightFactor})" points="0 0 -50 0 50 100 100 100" fill="Lightslategray" />
					<polygon transform="rotate({rotationAngle}) scale({scaleFactor}, {heightFactor})" points="-100 0 -50 0 50 100 00 100" fill="Darkslategray" />
				</g>
			{/each}
		{/each}

		<!-- <polygon transform="translate(0 200)" points="0 0 100 0 150 50 50 50" fill="hotpink" />
        <polygon transform="translate(200 200) rotate(90)" points="0 0 100 0 150 50 50 50" fill="dodgerblue" />

        <polygon transform="translate(150 150)" points="0 0 100 0 150 50 50 50" fill="hotpink" />
        <polygon transform="translate(350 150) rotate(90)" points="0 0 100 0 150 50 50 50" fill="dodgerblue" />

        <polygon transform="translate(300 100)" points="0 0 100 0 150 50 50 50" fill="hotpink" />
        <polygon transform="translate(500 100) rotate(90)" points="0 0 100 0 150 50 50 50" fill="dodgerblue" /> -->
	</svg>
</div>

<style>
	#control {
		display: flex;
		flex-direction: column;
		padding: 20px;
		gap: 20px;
	}

	.control-item {
		display: flex;
		flex-direction: column;
		gap: 5px;
	}

	.input-group {
		display: flex;
		gap: 10px;
		align-items: center;
	}

	.input-group input[type='range'] {
		flex: 1;
	}

	.input-group input[type='number'] {
		width: 80px;
		padding: 5px;
		border: 1px solid #ccc;
		border-radius: 4px;
	}

	.control-item label {
		font-size: 14px;
		font-weight: 500;
	}
</style>
