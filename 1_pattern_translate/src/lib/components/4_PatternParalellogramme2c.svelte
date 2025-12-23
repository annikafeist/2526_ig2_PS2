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

	let color1 = $derived(chroma.oklch(brightness1, 0.2, hue+40).hex());
	let color2 = $derived(chroma.oklch(brightness2, 0.2, hue+80).hex());
	let color3 = $derived(chroma.oklch(brightness3, 0.2, hue+120).hex());
	let color4 = $derived(chroma.oklch(brightness4, 0.2, hue+160).hex());
	// $inspect(color1);

	const squareCount = 20;
	const squareSize = 1000 / squareCount;
	const offset = squareSize / 4; // 45-Grad-Versatz

	let breite1 = $state(50);
	let breite2 = $state(50);
	let rotation = $state(0); // Rotation in Grad

	// Funktion zur Berechnung der Eckpunkte des rotierten Rechtecks
	function getRotatedRectCorners(rotation) {
		const rad = (rotation * Math.PI) / 180;
		const cos = Math.cos(rad);
		const sin = Math.sin(rad);
		
		// Die vier Ecken des Rechtecks (unrotiert)
		const corners = [
			{ x: 0, y: 0 },           // oben links
			{ x: breite1, y: 0 },     // oben rechts
			{ x: breite1, y: breite1 },  // unten rechts
			{ x: 0, y: breite1 }      // unten links
		];
		
		// Rotiere um den Mittelpunkt des Rechtecks
		const centerX = breite1 / 2;
		const centerY = breite1 / 2;
		
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
		<input id="breite1" type="range" min="33" max="100" step="0.1" bind:value={breite1} />
		<label for="breite1">Width1: {breite1.toFixed(2)}</label>
	</div>
	<div class="control-item">
		<input id="breite2" type="range" min="33" max="100" step="0.1" bind:value={breite2} />
		<label for="breite2">Width2: {breite2.toFixed(2)}</label>
	</div>
	<div class="control-item">
		<input id="rotation" type="range" min="0" max="360" step="1" bind:value={rotation} />
		<label for="rotation">Rotation (color4): {rotation}°</label>
	</div>
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
</div>

<div class="svg-container">
	<svg viewBox="-500 -500 1000 1000" class="svg-canvas">
		{#each Array(20) as _, j}
			<g transform="translate({(j - 10) * breite2} {(j - 10) * (breite1 + breite2)} )">
				{#each Array(20) as _, i}
					{@const corners = getRotatedRectCorners(rotation)}
					<g transform="translate({(i - 10) * (breite1 + breite2)} {(i - 10) * -breite2})">
					<!-- Rotiertes Rechteck (color4) -->
					<rect 
						transform="translate(0 0) rotate({rotation} {breite1/2} {breite1/2})" 
						width={breite1} 
						height={breite1} 
						fill={gradientMode ? getColor(brightness4, i, j, 160) : color4} 
					/>
					
					<polygon
						transform="translate(0 {breite1 + breite2})"
						points="0 {-breite2} {breite2} 0 0 {breite2} {-breite2} 0"
						fill={gradientMode ? getColor(brightness1, i, j, 40) : color1}
						/>
						<!-- Angepasstes Polygon (color2) - untere rechte Seite -->
						<polygon
							transform="translate(0)"
							points="{corners[3].x} {corners[3].y}, {corners[2].x} {corners[2].y}, {breite1 + breite2} {breite1 +
								breite2}, {breite2} {breite1 + breite2}"
							fill={gradientMode ? getColor(brightness2, i, j, 80) : color2}
						/>
						<!-- Angepasstes Polygon (color3) - obere rechte Seite -->
						<polygon
							transform="translate(0)"
							points="{corners[1].x} {corners[1].y}, {corners[2].x} {corners[2].y}, {breite1 + breite2} {breite1 -
								breite2}, {breite1 + breite2} {-breite2}"
							fill={gradientMode ? getColor(brightness3, i, j, 120) : color3}
						/>
					</g>

				{/each}
			</g>
		{/each}

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
</style>