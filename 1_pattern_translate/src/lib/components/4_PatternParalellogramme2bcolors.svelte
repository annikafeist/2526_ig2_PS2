<!-- 
  - HTML und Svelte Code zeigen + Funktionsaufrufe
  - Zeige wie das erste kleine Pattern beginnt (außerhalb der Schleife)

  - 2 Rechtecke in der Schleife und entsprechend der Indizes x, y, width und height anpassen
  - Funktionen müssen sie schreiben 
  - Kleiner Tipp mit data-x um zu sehen welche Indizes die Rects haben
-->

<!-- eventuell helligkeit wechsel schiebe regler 
 	1. farbpaletten einbauen
	gruppierung abbilde in den farben und dann umrandet wenn ausgewählt
	2. rotation der einzelnen quadrate ohne gruppierung
	   
	2. -->

<script>
	import chroma from 'chroma-js';

	let hue = $state(120);
	let invertBrightness = $state(false); // false = normal, true = invertiert
	let gradientMode = $state(false); // false = statisch, true = Farbverlauf
	let colorPaletteActive = $state(false); // Toggle für die Farbpalette
	let dynamicColorMode = $state(true); // Toggle für dynamische Farben

	// Farbpalette
	const paletteColors = {
		ebony: '#555D50',
		artichoke: '#8A9A5B',
		antiqueBrass: '#CD7F32',
		desertSand: '#EDC9AF'
	};

	// Funktion zum Umschalten zwischen den Modi
	function togglePalette() {
		colorPaletteActive = true;
		dynamicColorMode = false;
	}

	function toggleDynamicMode() {
		dynamicColorMode = true;
		colorPaletteActive = false;
	}

	// Dynamische Helligkeitswerte die zwischen normal und invertiert wechseln
	let brightness1 = $derived(invertBrightness ? 0.9 : 0.4);
	let brightness2 = $derived(invertBrightness ? 0.7 : 0.6);
	let brightness3 = $derived(invertBrightness ? 0.6 : 0.7);
	let brightness4 = $derived(invertBrightness ? 0.4 : 0.9);

	// Funktion zur Berechnung der Farbe basierend auf Position (für Farbverlauf)
	function getColor(baseHue, i, j, offset) {
		if (gradientMode) {
			// Berechne Farbshift basierend auf Position im Grid
			const position = (i + j) / 40; // Normalisiert auf 0-1
			const hueShift = position * 360; // Feste Intensität von 360
			return chroma.oklch(baseHue, 0.2, hue + offset + hueShift).hex();
		}
		return chroma.oklch(baseHue, 0.2, hue + offset).hex();
	}

	let color1 = $derived(colorPaletteActive ? paletteColors.ebony : chroma.oklch(brightness1, 0.2, hue+40).hex());
	let color2 = $derived(colorPaletteActive ? paletteColors.artichoke : chroma.oklch(brightness2, 0.2, hue+80).hex());
	let color3 = $derived(colorPaletteActive ? paletteColors.antiqueBrass : chroma.oklch(brightness3, 0.2, hue+120).hex());
	let color4 = $derived(colorPaletteActive ? paletteColors.desertSand : chroma.oklch(brightness4, 0.2, hue+160).hex());
	// $inspect(color1);

	const squareCount = 20;
	const squareSize = 1000 / squareCount;
	const offset = squareSize / 4; // 45-Grad-Versatz

	let rotation = $state(0); // Rotation in Grad

	// Funktion zur Berechnung der Eckpunkte des rotierten Rechtecks
	function getRotatedRectCorners(rotation) {
		const rad = (rotation * Math.PI) / 180;
		const cos = Math.cos(rad);
		const sin = Math.sin(rad);
		
		// Die vier Ecken des Rechtecks (unrotiert)
		const corners = [
			{ x: 0, y: 0 },           // oben links
			{ x: 50, y: 0 },     // oben rechts
			{ x: 50, y: 50 },  // unten rechts
			{ x: 0, y: 50 }      // unten links
		];
		
		// Rotiere um den Mittelpunkt des Rechtecks
		const centerX = 50 / 2;
		const centerY = 50 / 2;
		
		return corners.map(corner => ({
			x: centerX + (corner.x - centerX) * cos - (corner.y - centerY) * sin,
			y: centerY + (corner.x - centerX) * sin + (corner.y - centerY) * cos
		}));
	}

	// function calculateSizeCords1(xi, yi) {

	// }

	// function calculateSizeCords2(xi, yi) {

	// }
</script>

<div id="control">
	<div class="control-item">
		<label>Farbmodus</label>
		<div class="button-group">
			<button 
				class="palette-button" 
				class:active={colorPaletteActive}
				onclick={togglePalette}
				title="Farbpalette"
			>
				<svg viewBox="0 0 100 100" class="palette-preview">
					<rect x="0" y="0" width="50" height="50" fill={paletteColors.desertSand} />
					<polygon points="0,50 25,75 0,100 -25,75" fill={paletteColors.ebony} />
					<polygon points="0,50 50,50 75,75 25,75" fill={paletteColors.artichoke} />
					<polygon points="50,0 50,50 75,25 75,-25" fill={paletteColors.antiqueBrass} />
				</svg>
			</button>
			<button 
				class="palette-button" 
				class:active={dynamicColorMode}
				onclick={toggleDynamicMode}
				title="Dynamische Farben"
			>
				<svg viewBox="0 0 100 100" class="palette-preview">
					<rect x="0" y="0" width="50" height="50" fill={chroma.oklch(brightness4, 0.2, hue+160).hex()} />
					<polygon points="0,50 25,75 0,100 -25,75" fill={chroma.oklch(brightness1, 0.2, hue+40).hex()} />
					<polygon points="0,50 50,50 75,75 25,75" fill={chroma.oklch(brightness2, 0.2, hue+80).hex()} />
					<polygon points="50,0 50,50 75,25 75,-25" fill={chroma.oklch(brightness3, 0.2, hue+120).hex()} />
				</svg>
			</button>
		</div>
	</div>
	{#if dynamicColorMode}
	<div class="control-item">
		<input id="hue" type="range" min="120" max="360" step="1" bind:value={hue} />
		<label for="hue">Color (hue): {hue}</label>
	</div>	<div class="control-item">
		<label for="brightness">invert</label>
		<div class="switch-container">
			<input id="brightness" type="checkbox" bind:checked={invertBrightness} class="switch" />
			<label for="brightness" class="switch-label"></label>
		</div>
	</div>
	<div class="control-item">
		<label for="gradient">Farbverlauf</label>
		<div class="switch-container">
			<input id="gradient" type="checkbox" bind:checked={gradientMode} class="switch" />
			<label for="gradient" class="switch-label"></label>
		</div>
	</div>
	{/if}
</div>

<div class="svg-container">
	<svg viewBox="-500 -500 1000 1000" class="svg-canvas">
		{#each Array(20) as _, j}
			<g transform="translate({(j - 10) * 50} {(j - 10) * (50 + 50)} )">
				{#each Array(20) as _, i}
					<g transform="translate({(i - 10) * (50 + 50)} {(i - 10) * -50})">
					<rect transform="translate(0 0)" width={50} height={50} fill={gradientMode ? getColor(brightness4, i, j, 160) : color4} />
					<polygon
						transform="translate(0 {50 + 50})"
						points="0 {-50} {50} 0 0 {50} {-50} 0"
						fill={gradientMode ? getColor(brightness1, i, j, 40) : color1}
						/>
						<polygon
							transform="translate(0)"
							points="0 {50}, {50} {50}, {50 + 50} {50 +
								50}, {50} {50 + 50}"
							fill={gradientMode ? getColor(brightness2, i, j, 80) : color2}
						/>
						<polygon
							transform="translate(0)"
							points="{50} 0, {50} {50}, {50 + 50} {50 -
								50}, {50 + 50} {-50}"
							fill={gradientMode ? getColor(brightness3, i, j, 120) : color3}
						/>
					</g>

					<!-- <polygon transform="translate({(50+50)*i+50+50} {(50+50)*i+50})" points="0 {-50} {50} 0 0 {50} {-50} 0" fill="#0ff" /> -->

					<!-- <polygon transform="translate({-400+i*150} {0-i*50}) rotate({0}) " points="0 0 100 0 150 50 50 50" fill="hotpink" />
                    <polygon transform="translate({-200+i*150} {0-i*50}) rotate({90}) " points="0 0 100 0 150 50 50 50" fill="dodgerblue" />
                    <rect transform="translate({-350+i*150} {50-i*50})" width={50} height={50} fill="#ff0" />
                    <polygon transform="translate({-400+i*150} {0-i*50})" points="0 {-50} {50} 0 0 {50} {-50} 0" fill="#0ff" /> -->
				{/each}
			</g>
		{/each}

		<!-- <rect transform="translate({200} {200})" width={50} height={50} fill="#ff0" />
		<polygon transform="translate({200} {200+50+50})" points="0 {-50} {50} 0 0 {50} {-50} 0" fill="#0ff" />

		<rect transform="translate({200+50+50} {200-50})" width={50} height={50} fill="#ff0" />
		<polygon transform="translate({200+50+50} {200+50+50-50})" points="0 {-50} {50} 0 0 {50} {-50} 0" fill="#0ff" /> -->

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

	.control-item input {
		width: 100%;
	}

	.control-item label {
		font-size: 14px;
	}

	.switch-container {
		position: relative;
		width: 60px;
		height: 30px;
	}

	.switch {
		opacity: 0;
		width: 0;
		height: 0;
	}

	.switch-label {
		position: absolute;
		cursor: pointer;
		top: 0;
		left: 0;
		right: 0;
		bottom: 0;
		background-color: #ccc;
		transition: 0.4s;
		border-radius: 30px;
	}

	.switch-label::before {
		position: absolute;
		content: "";
		height: 22px;
		width: 22px;
		left: 4px;
		bottom: 4px;
		background-color: white;
		transition: 0.4s;
		border-radius: 50%;
	}

	.switch:checked + .switch-label {
		background-color: #4CAF50;
	}

	.switch:checked + .switch-label::before {
		transform: translateX(30px);
	}

	.palette-button {
		width: 80px;
		height: 80px;
		border: 3px solid transparent;
		border-radius: 8px;
		background: white;
		cursor: pointer;
		transition: all 0.3s ease;
		padding: 5px;
	}

	.palette-button:hover {
		transform: scale(1.05);
	}

	.palette-button.active {
		border-color: #4CAF50;
		box-shadow: 0 0 10px rgba(76, 175, 80, 0.5);
	}

	.palette-preview {
		width: 100%;
		height: 100%;
		display: block;
	}

	.button-group {
		display: flex;
		gap: 10px;
		flex-wrap: wrap;
	}
</style>
