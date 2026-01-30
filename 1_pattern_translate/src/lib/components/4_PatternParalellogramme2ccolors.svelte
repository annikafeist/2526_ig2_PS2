<!-- 
  - HTML und Svelte Code zeigen + Funktionsaufrufe
  - Zeige wie das erste kleine Pattern beginnt (außerhalb der Schleife)

  - 2 Rechtecke in der Schleife und entsprechend der Indizes x, y, width und height anpassen
  - Funktionen müssen sie schreiben 
  - Kleiner Tipp mit data-x um zu sehen welche Indizes die Rects haben
-->


<script>
	import chroma from 'chroma-js';
	import Toggle from '$lib/ui/Toggle.svelte';
	import Slider from '$lib/ui/Slider.svelte';

	let invertBrightness = $state(false); // false = normal, true = invertiert
	let gradientIntensity = $state(0); // 0 = kein Farbverlauf, höher = stärkerer Verlauf

	// Vordefinierte Farbpaletten
	const colorPalettes = [
		{
			name: 'Earth Tones',
			colors: ['#555D50', '#8A9A5B', '#CD7F32', '#EDC9AF']
		},
		{
			name: 'Black and White',
			colors: ['#000000', '#444444', '#808080', '#FFFFFF']
		},
		{
			name: 'Sunset Warm',
			colors: ['#8B2635', '#C84630', '#E87A3E', '#F4B860']
		},
		{
			name: 'Random',
			colors: ['#FF5733', '#33FF57', '#3357FF', '#F333FF']
		}
	];

	let selectedPaletteIndex = $state(0);
	let randomColors = $state(['#FF5733', '#33FF57', '#3357FF', '#F333FF']);

	// Funktion zum Generieren zufälliger Farben
	function generateRandomColors() {
		return [
			chroma.random().hex(),
			chroma.random().hex(),
			chroma.random().hex(),
			chroma.random().hex()
		];
	}

	// Funktion zum Auswählen einer Palette
	function selectPalette(index) {
		selectedPaletteIndex = index;
		if (index === 3) {
			// Random palette
			randomColors = generateRandomColors();
		}
	}

	let paletteColors = $derived(
		selectedPaletteIndex === 3 ? randomColors : colorPalettes[selectedPaletteIndex].colors
	);

	// Funktion zum Anpassen der Farbe basierend auf Helligkeit
	function adjustColor(baseColor, hueShift = 0) {
		let color = chroma(baseColor);

		// Hue-Anpassung
		if (hueShift !== 0) {
			const [l, c, h] = color.oklch();
			color = chroma.oklch(l, c, (h || 0) + hueShift);
		}

		// Helligkeit invertieren
		if (invertBrightness) {
			const [l, c, h] = color.oklch();
			// Invertiere die Helligkeit (0.9 wird zu ~0.4, 0.4 wird zu ~0.9)
			const newL = 1.3 - l;
			color = chroma.oklch(Math.max(0.2, Math.min(0.95, newL)), c, h);
		}

		return color.hex();
	}

	// Funktion zur Berechnung der Farbe basierend auf Position (für Farbverlauf)
	function getColor(baseColor, i, j) {
		if (gradientIntensity > 0) {
			// Berechne Farbshift basierend auf Position im Grid
			const position = (i + j) / 40; // Normalisiert auf 0-1
			const hueShift = position * gradientIntensity; // Intensität vom Slider
			return adjustColor(baseColor, hueShift);
		}
		return adjustColor(baseColor, 0);
	}

	let color1 = $derived(paletteColors[0]);
	let color2 = $derived(paletteColors[1]);
	let color3 = $derived(paletteColors[2]);
	let color4 = $derived(paletteColors[3]);
	// $inspect(color1);

	const squareCount = 20;
	const squareSize = 1000 / squareCount;
	const offset = squareSize / 4; // 45-Grad-Versatz

	let breite1 = $state(50);
	let breite2 = $state(50);
	let rotation = $state(0); // Rotation in Grad
	const scale = 1; // Skalierung
	const spacing = 100; // Abstand zwischen Elementen

	// Funktion zur Berechnung der Eckpunkte des rotierten Rechtecks
	function getRotatedRectCorners(rotation) {
		const rad = (rotation * Math.PI) / 180;
		const cos = Math.cos(rad);
		const sin = Math.sin(rad);

		// Die vier Ecken des Rechtecks (unrotiert)
		const corners = [
			{ x: 0, y: 0 }, // oben links
			{ x: breite1, y: 0 }, // oben rechts
			{ x: breite1, y: breite1 }, // unten rechts
			{ x: 0, y: breite1 } // unten links
		];

		// Rotiere um den Mittelpunkt des Rechtecks
		const centerX = breite1 / 2;
		const centerY = breite1 / 2;

		return corners.map((corner) => ({
			x: centerX + (corner.x - centerX) * cos - (corner.y - centerY) * sin,
			y: centerY + (corner.x - centerX) * sin + (corner.y - centerY) * cos
		}));
	}
</script>

<div class="svg-container">
	<svg viewBox="-500 -500 1000 1000" class="svg-canvas">
		{#each Array(20) as _, j}
			<g
				transform="translate({(j - 10) * breite2 * (spacing / 100)} {(j - 10) *
					(breite1 + breite2) *
					(spacing / 100)} )"
			>
				{#each Array(20) as _, i}
					{@const corners = getRotatedRectCorners(rotation)}
					{@const nextCorners = getRotatedRectCorners(rotation)}
					{@const rightCorners = getRotatedRectCorners(rotation)}
					{@const bottomCorners = getRotatedRectCorners(rotation)}
					{@const bottomRightCorners = getRotatedRectCorners(rotation)}
					<g
						transform="translate({(i - 10) * (breite1 + breite2) * (spacing / 100)} {(i - 10) *
							-breite2 *
							(spacing / 100)})"
					>
						<!-- Rotiertes Rechteck (color4) -->
						<rect
							transform="translate(0 0) rotate({rotation} {breite1 / 2} {breite1 / 2})"
							width={breite1}
							height={breite1}
							fill={getColor(color4, i, j)}
						/>

						<polygon
							transform="translate(0)"
							points="{corners[2].x} {corners[2].y}, {bottomCorners[1].x +
								breite2} {bottomCorners[1].y + (breite1 + breite2)}, {bottomRightCorners[0].x +
								(breite1 + breite2) +
								breite2} {bottomRightCorners[0].y + (breite1 + breite2) - breite2}, {rightCorners[3]
								.x +
								(breite1 + breite2)} {rightCorners[3].y - breite2}"
							fill={getColor(color1, i, j)}
						/>
						<!-- Angepasstes Polygon (color2) - untere rechte Seite -->
						<polygon
							transform="translate(0)"
							points="{corners[3].x} {corners[3].y}, {corners[2].x} {corners[2].y}, {nextCorners[1]
								.x + breite2} {nextCorners[1].y + (breite1 + breite2)}, {nextCorners[0].x +
								breite2} {nextCorners[0].y + (breite1 + breite2)},"
							fill={getColor(color2, i, j)}
						/>
						<!-- Angepasstes Polygon (color3) - obere rechte Seite -->
						<polygon
							transform="translate(0)"
							points="{corners[1].x} {corners[1].y}, {corners[2].x} {corners[2].y}, {nextCorners[3]
								.x +
								(breite1 + breite2)} {nextCorners[3].y - breite2}, {nextCorners[0].x +
								(breite1 + breite2)} {nextCorners[0].y - breite2}"
							fill={getColor(color3, i, j)}
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

<div class="sidebar-right">
	<div class="control-item">
		<Slider
			bind:value={breite1}
			min={30}
			max={100}
			step={0.1}
			label="Element Size:"
			color={color4}
		/>
		<Slider
			bind:value={breite2}
			min={30}
			max={100}
			step={0.1}
			label="Element Size:"
			color={color1}
		/>
		<Slider
			bind:value={rotation}
			min={-90}
			max={37}
			step={1}
			snapValues={[-26, 0]}
			label="Rotation: {rotation}°"
		/>
		
		<label>choose color palette</label>
		<div class="palette-selector">
			{#each colorPalettes as palette, index}
				<button
					class="palette-button"
					class:selected={selectedPaletteIndex === index}
					onclick={() => selectPalette(index)}
				>
					<div class="palette-preview">
						{#each index === 3 ? randomColors : palette.colors as color}
							<div class="color-swatch" style="background-color: {color}"></div>
						{/each}
					</div>
					<span class="palette-name">{palette.name}</span>
				</button>
			{/each}
		</div>
	</div>
</div>

<!-- Eventuell eine seite machen bei der man feste farben hat und die dann immer den elementen zuweisen lassen
 um zu gucken wie farben auf das muster wirken können
 und die größe und rotation soll an alle seiten-->

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
		gap: 8px;
	}

	.control-item label {
		font-size: 14px;
		font-weight: 500;
	}

	.palette-selector {
		display: grid;
		grid-template-columns: 1fr 1fr;
		gap: 10px;
	}

	.palette-button {
		display: flex;
		flex-direction: column;
		align-items: center;
		gap: 8px;
		padding: 12px;
		background: rgb(40, 40, 40);
		border: 2px solid #555;
		border-radius: 8px;
		cursor: pointer;
		transition: all 0.2s ease;
		min-width: 130px;
	}

	.palette-button:hover {
		border-color: #999;
		transform: translateY(-2px);
		box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
	}

	.palette-button.selected {
		border-color: #acacac;
		border-width: 3px;
		background: rgb(40, 40, 40);
		box-shadow: 0 4px 12px rgba(74, 144, 226, 0.2);
	}

	.palette-preview {
		display: flex;
		gap: 4px;
		width: 100%;
	}

	.color-swatch {
		flex: 1;
		height: 40px;
		border-radius: 4px;
		border: 1px solid rgba(0, 0, 0, 0.1);
	}

	.palette-name {
		font-size: 12px;
		font-weight: 500;
		color: inherit;
		white-space: nowrap;
		overflow: hidden;
		text-overflow: ellipsis;
		width: 100%;
		text-align: center;
	}

	.palette-button.selected .palette-name {
		color: #acacac;
		font-weight: 600;
	}
</style>
