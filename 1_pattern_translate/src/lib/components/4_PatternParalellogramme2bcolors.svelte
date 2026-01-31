<script>
	import chroma from 'chroma-js';
	import Toggle from '$lib/ui/Toggle.svelte';
	import Slider from '$lib/ui/Slider.svelte';
	import EditableColorPalette from '$lib/ui/EditableColorPalette.svelte';

	let invertBrightness = $state(false); // false = normal, true = invertiert
	let gradientIntensity = $state(0); // 0 = kein Farbverlauf, höher = stärkerer Verlauf

	// Farbpalette
	let paletteColors = $state(['#555D50', '#8A9A5B', '#CD7F32', '#EDC9AF']);
	let selectedColorIndex = $state(0);

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
						<!-- Polygon (color2) - untere rechte Seite -->
						<polygon
							transform="translate(0)"
							points="{corners[3].x} {corners[3].y}, {corners[2].x} {corners[2].y}, {nextCorners[1]
								.x + breite2} {nextCorners[1].y + (breite1 + breite2)}, {nextCorners[0].x +
								breite2} {nextCorners[0].y + (breite1 + breite2)},"
							fill={getColor(color2, i, j)}
						/>
						<!-- Polygon (color3) - obere rechte Seite -->
						<polygon
							transform="translate(0)"
							points="{corners[1].x} {corners[1].y}, {corners[2].x} {corners[2].y}, {nextCorners[3]
								.x +
								(breite1 + breite2)} {nextCorners[3].y - breite2}, {nextCorners[0].x +
								(breite1 + breite2)} {nextCorners[0].y - breite2}"
							fill={getColor(color3, i, j)}
						/>
					</g>
				{/each}
			</g>
		{/each}
	</svg>
</div>

<div class="sidebar-right">
	<div class="control-item">
		<Slider
			bind:value={breite2}
			min={30}
			max={100}
			step={0.1}
			label="Element size:"
			color={color2}
		/>
		<Slider
			bind:value={breite1}
			min={30}
			max={100}
			step={0.1}
			label="Element size:"
			color={color4}
		/>
		<Slider
			bind:value={rotation}
			min={-90}
			max={37}
			step={1}
			snapValues={[-26, 0]}
			label="Rotation:"
		/>
		<label>Edit Color Palette</label>
		<EditableColorPalette
			bind:colors={paletteColors}
			bind:selectedColorIndex
			width={200}
			swatchSize={35}
		/>
	</div>
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
		gap: 8px;
	}

	.control-item label {
		font-size: 14px;
		font-weight: 500;
	}
</style>
