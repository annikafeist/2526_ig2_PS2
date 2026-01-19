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

	let invertBrightness = $state(false); // false = normal, true = invertiert
	let gradientIntensity = $state(0); // 0 = kein Farbverlauf, höher = stärkerer Verlauf

	// Zwei separate Hue-Slider mit gekoppelter Bewegung
	let hue1 = $state(0); // Farbton für Element 1 und 3 (0-360)
	let hue2 = $state(40); // Farbton für Element 2 und 4 (0-360)
	
	// Track welcher Slider zuletzt bewegt wurde
	let lastChangedHue1 = $state(0);
	let lastChangedHue2 = $state(40);
	
	// Wenn hue1 sich ändert, aktualisiere hue2
	$effect(() => {
		if (hue1 !== lastChangedHue1) {
			lastChangedHue1 = hue1;
			// Begrenze hue2 auf den Bereich [0, 360]
			const newHue2 = hue1 + 40;
			hue2 = Math.max(0, Math.min(360, newHue2));
			lastChangedHue2 = hue2;
		}
	});
	
	// Wenn hue2 sich ändert, aktualisiere hue1
	$effect(() => {
		if (hue2 !== lastChangedHue2) {
			lastChangedHue2 = hue2;
			// Begrenze hue1 auf den Bereich [0, 360]
			const newHue1 = hue2 - 40;
			hue1 = Math.max(0, Math.min(360, newHue1));
			lastChangedHue1 = hue1;
		}
	});

	// Feste Saturation und Lightness für jedes der 4 Elemente
	// Format: [Saturation (0-100), Lightness (0-100)]
	const element1_SL = [100, 35]; // Mittel-dunkel, gesättigt
	const element2_SL = [80, 60]; // Heller, weniger gesättigt
	const element3_SL = [100, 15]; // Dunkel, sehr gesättigt
	const element4_SL = [70, 80]; // Sehr hell, wenig gesättigt

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

	const rotation = 0; // Rotation in Grad
	const scale = 1; // Skalierung
	const spacing = 100; // Abstand zwischen Elementen
</script>

<div id="control">
	<div class="control-item">
		<Slider bind:value={hue1} min={0} max={360} step={1} label="Hue1: {Math.round(hue1)}°" />
	</div>

	<div class="control-item">
		<Slider bind:value={hue2} min={0} max={360} step={1} label="Hue2: {Math.round(hue2)}°" />
	</div>

	<div class="control-item">
		<label for="brightness">Helligkeit invertieren</label>
		<Toggle bind:value={invertBrightness} label="Invert" />
	</div>
	<Slider
		bind:value={gradientIntensity}
		min={0}
		max={360}
		step={1}
		label="Farbverlauf-Intensität: {gradientIntensity.toFixed(0)}"
	/>
</div>

<div class="svg-container">
	<svg viewBox="-500 -500 1000 1000" class="svg-canvas">
		{#each Array(20) as _, j}
			<g
				transform="translate({(j - 10) * 50 * (spacing / 100)} {(j - 10) *
					(50 + 50) *
					(spacing / 100)} )"
			>
				{#each Array(20) as _, i}
					<g
						transform="translate({(i - 10) * (50 + 50) * (spacing / 100)} {(i - 10) *
							-50 *
							(spacing / 100)}) rotate({rotation}) scale({scale})"
					>
						<rect
							transform="translate(0 0)"
							width={50}
							height={50}
							fill={getColor(hue2, element4_SL[0], element4_SL[1], i, j)}
						/>
						<polygon
							transform="translate(0 {50 + 50})"
							points="0 {-50} {50} 0 0 {50} {-50} 0"
							fill={getColor(hue1, element1_SL[0], element1_SL[1], i, j)}
						/>
						<polygon
							transform="translate(0)"
							points="0 {50}, {50} {50}, {50 + 50} {50 + 50}, {50} {50 + 50}"
							fill={getColor(hue2, element2_SL[0], element2_SL[1], i, j)}
						/>
						<polygon
							transform="translate(0)"
							points="{50} 0, {50} {50}, {50 + 50} {50 - 50}, {50 + 50} {-50}"
							fill={getColor(hue1, element3_SL[0], element3_SL[1], i, j)}
						/>
					</g>
				{/each}
			</g>
		{/each}
	</svg>
</div>

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

<!-- Farbpaletten mit festen Farben, dafür kann man dann zb Saturation oder luminess bearbeiten -->
