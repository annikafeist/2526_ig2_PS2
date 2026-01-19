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

	const rotation = 0; // Rotation in Grad
	const scale = 1; // Skalierung
	const spacing = 100; // Abstand zwischen Elementen
</script>

<div id="control">
	<div class="control-item">
		<label>Farbpalette bearbeiten</label>
		<EditableColorPalette bind:colors={paletteColors} bind:selectedColorIndex width={200} swatchSize={35} />
	</div>

	<div class="control-item">
		<label for="brightness">Helligkeit invertieren</label>
		<Toggle bind:value={invertBrightness} label="Invert" />
	</div>
	<Slider bind:value={gradientIntensity} min={0} max={360} step={1} label="Farbverlauf-Intensität: {gradientIntensity.toFixed(0)}" />
</div>

<div class="svg-container">
	<svg viewBox="-500 -500 1000 1000" class="svg-canvas">
		{#each Array(20) as _, j}
			<g transform="translate({(j - 10) * 50 * (spacing / 100)} {(j - 10) * (50 + 50) * (spacing / 100)} )">
				{#each Array(20) as _, i}
					<g transform="translate({(i - 10) * (50 + 50) * (spacing / 100)} {(i - 10) * -50 * (spacing / 100)}) rotate({rotation}) scale({scale})">
					<rect transform="translate(0 0)" width={50} height={50} fill={getColor(color4, i, j)} />
					<polygon
						transform="translate(0 {50 + 50})"
						points="0 {-50} {50} 0 0 {50} {-50} 0"
						fill={getColor(color1, i, j)}
						/>
						<polygon
							transform="translate(0)"
							points="0 {50}, {50} {50}, {50 + 50} {50 +
								50}, {50} {50 + 50}"
							fill={getColor(color2, i, j)}
						/>
						<polygon
							transform="translate(0)"
							points="{50} 0, {50} {50}, {50 + 50} {50 -
								50}, {50 + 50} {-50}"
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

<!-- farben nicht einzeln bearbeitbar für jedes element sondern eine feste farbe 
 und jedes element hat eventuell eine andere helligkeit und saturation und so -->