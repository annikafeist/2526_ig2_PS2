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

	// Ein Hue-Slider für beide Farbtöne
	let hue1 = $state(0); // Farbton für Element 1 und 3 (0-360)
	let hue2 = $derived((hue1 + 40) % 360); // Farbton für Element 2 und 4, immer 40° mehr als hue1

	// Feste Saturation und Lightness für jedes der 4 Elemente
	// Format: [Saturation (0-100), Lightness (0-100)]
	const element1_SL = [100, 35]; // Mittel-dunkel, gesättigt
	const element2_SL = [70, 80]; // Heller, weniger gesättigt
	const element3_SL = [100, 15]; // Dunkel, sehr gesättigt
	const element4_SL = [80, 60]; // Sehr hell, wenig gesättigt

	// Funktion zum Erstellen einer HSL-Farbe aus Hue und fester Saturation/Lightness
	function createColorFromHue(hue, saturation, lightness, hueShift = 0) {
		let finalHue = (hue + hueShift) % 360;
		let color = chroma.hsl(finalHue, saturation / 100, lightness / 100);

		// Helligkeit invertieren
		if (invertBrightness) {
			const [l, c, h] = color.oklch();
			const newL = 1.3 - l;
			color = chroma.oklch(Math.max(0.2, Math.min(0.95, newL)), c, h);
		}

		return color.hex();
	}

	// Funktion zur Berechnung der Farbe basierend auf Position (für Farbverlauf)
	function getColor(hue, saturation, lightness, i, j) {
		if (gradientIntensity > 0) {
			// Berechne Farbshift basierend auf Position im Grid
			const position = (i + j) / 40; // Normalisiert auf 0-1
			const hueShift = position * gradientIntensity; // Intensität vom Slider
			return createColorFromHue(hue, saturation, lightness, hueShift);
		}
		return createColorFromHue(hue, saturation, lightness, 0);
	}

	// Derived colors für Vorschau
	let color1 = $derived(createColorFromHue(hue1, element1_SL[0], element1_SL[1]));
	let color2 = $derived(createColorFromHue(hue2, element2_SL[0], element2_SL[1]));
	let color3 = $derived(createColorFromHue(hue1, element3_SL[0], element3_SL[1]));
	let color4 = $derived(createColorFromHue(hue2, element4_SL[0], element4_SL[1]));
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
							fill={getColor(hue2, element4_SL[0], element4_SL[1], i, j)}
						/>

						<polygon
							transform="translate(0)"
							points="{corners[2].x} {corners[2].y}, {bottomCorners[1].x +
								breite2} {bottomCorners[1].y + (breite1 + breite2)}, {bottomRightCorners[0].x +
								(breite1 + breite2) +
								breite2} {bottomRightCorners[0].y + (breite1 + breite2) - breite2}, {rightCorners[3]
								.x +
								(breite1 + breite2)} {rightCorners[3].y - breite2}"
							fill={getColor(hue1, element1_SL[0], element1_SL[1], i, j)}
						/>
						<!-- Angepasstes Polygon (color2) - untere rechte Seite -->
						<polygon
							transform="translate(0)"
							points="{corners[3].x} {corners[3].y}, {corners[2].x} {corners[2].y}, {nextCorners[1]
								.x + breite2} {nextCorners[1].y + (breite1 + breite2)}, {nextCorners[0].x +
								breite2} {nextCorners[0].y + (breite1 + breite2)},"
							fill={getColor(hue2, element2_SL[0], element2_SL[1], i, j)}
						/>
						<!-- Angepasstes Polygon (color3) - obere rechte Seite -->
						<polygon
							transform="translate(0)"
							points="{corners[1].x} {corners[1].y}, {corners[2].x} {corners[2].y}, {nextCorners[3]
								.x +
								(breite1 + breite2)} {nextCorners[3].y - breite2}, {nextCorners[0].x +
								(breite1 + breite2)} {nextCorners[0].y - breite2}"
							fill={getColor(hue1, element3_SL[0], element3_SL[1], i, j)}
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
			color={color1}
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
			label="Rotation: {rotation}°"
		/>
		<Slider bind:value={hue1} min={0} max={360} step={1} label="Hue: {Math.round(hue1)}°" />
		<Slider
			bind:value={gradientIntensity}
			min={0}
			max={360}
			step={1}
			label="Gradient intensity: {gradientIntensity.toFixed(0)}"
		/>
	</div>

	<div class="control-item invert-toggle">
		<!-- <label for="brightness">Helligkeit invertieren</label> -->
		<Toggle bind:value={invertBrightness} label="Invert" />
	</div>
</div>

<!-- Farbpaletten mit festen Farben, dafür kann man dann zb Saturation oder luminess bearbeiten -->

<!-- farben nicht einzeln bearbeitbar für jedes element sondern eine feste farbe 
 und jedes element hat eventuell eine andere helligkeit und saturation und so -->

<style>
	#control {
		display: flex;
		flex-direction: column;
		padding: 20px;
		gap: 20px;
		width: 250px;
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

	.invert-toggle {
		margin-top: 8px;
	}

	.color-preview {
		display: flex;
		gap: 10px;
		flex-wrap: wrap;
	}

	.preview-swatch {
		width: 80px;
		height: 80px;
		border-radius: 8px;
		display: flex;
		align-items: flex-end;
		justify-content: center;
		padding: 8px;
		box-shadow: 0 2px 8px rgba(0, 0, 0, 0.15);
		border: 2px solid rgba(0, 0, 0, 0.1);
	}

	.preview-swatch span {
		font-size: 10px;
		font-weight: 600;
		color: white;
		text-shadow: 0 1px 2px rgba(0, 0, 0, 0.5);
		background: rgba(0, 0, 0, 0.3);
		padding: 2px 6px;
		border-radius: 4px;
	}
</style>
