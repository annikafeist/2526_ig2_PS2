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
	import Slider from '$lib/ui/Slider.svelte';

	// Fixed brightness values
	let brightness1 = 0.4;
	let brightness2 = 0.6;
	let brightness3 = 0.7;
	let brightness4 = 0.9;

	// Fixed hue value
	const hue = 120;

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

<div class="sidebar-right">
<div id="control">
	<Slider bind:value={breite1} min={30} max={100} step={0.1} label="Width1: {breite1.toFixed(2)}" />
	<Slider bind:value={breite2} min={30} max={100} step={0.1} label="Width2: {breite2.toFixed(2)}" />
	<Slider bind:value={rotation} min={-90} max={37} step={1} snapValues={[-26, 0]} label="Rotation: {rotation}°" />
</div>
</div>


<div class="svg-container">
	<svg viewBox="-500 -500 1000 1000" class="svg-canvas">
		{#each Array(20) as _, j}
			<g transform="translate({(j - 10) * breite2} {(j - 10) * (breite1 + breite2)} )">
				{#each Array(20) as _, i}
					{@const corners = getRotatedRectCorners(rotation)}
					{@const nextCorners = getRotatedRectCorners(rotation)}
					{@const rightCorners = getRotatedRectCorners(rotation)}
					{@const bottomCorners = getRotatedRectCorners(rotation)}
					{@const bottomRightCorners = getRotatedRectCorners(rotation)}
					<g transform="translate({(i - 10) * (breite1 + breite2)} {(i - 10) * -breite2})">
					<!-- Rotiertes Rechteck (color4) -->
					<rect 
						transform="translate(0 0) rotate({rotation} {breite1/2} {breite1/2})" 
						width={breite1} 
						height={breite1} 
						fill={color4} 
					/>
					
					<polygon
						transform="translate(0)"
						points="{corners[2].x} {corners[2].y}, {bottomCorners[1].x + breite2} {bottomCorners[1].y + (breite1 + breite2)}, {bottomRightCorners[0].x + (breite1 + breite2) + breite2} {bottomRightCorners[0].y + (breite1 + breite2) - breite2}, {rightCorners[3].x + (breite1 + breite2)} {rightCorners[3].y - breite2}"
						fill={color1}
						/>
						<!-- Angepasstes Polygon (color2) - untere rechte Seite -->
						<polygon
							transform="translate(0)"
							points="{corners[3].x} {corners[3].y}, {corners[2].x} {corners[2].y}, {nextCorners[1].x + breite2} {nextCorners[1].y + (breite1 + breite2)}, {nextCorners[0].x + breite2} {nextCorners[0].y + (breite1 + breite2)},"
							fill={color2}
						/>
						<!-- Angepasstes Polygon (color3) - obere rechte Seite -->
						<polygon
							transform="translate(0)"
							points="{corners[1].x} {corners[1].y}, {corners[2].x} {corners[2].y}, {nextCorners[3].x + (breite1 + breite2)} {nextCorners[3].y - breite2}, {nextCorners[0].x + (breite1 + breite2)} {nextCorners[0].y - breite2}"
							fill={color3}
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
		width: 300px;
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
</style>


