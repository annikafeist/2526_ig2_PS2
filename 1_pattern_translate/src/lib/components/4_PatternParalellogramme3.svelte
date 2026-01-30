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

	// Available colors for selection
	const availableColors = ['#A2E8A0', '#5D9140', '#00BA61', '#00522A'];
	
	// Selected color indices for each tile
	let selectedColor1 = $state(0);
	let selectedColor2 = $state(1);
	let selectedColor3 = $state(2);
	let selectedColor4 = $state(3);

	// Track which dropdown is open
	let dropdown1Open = $state(false);
	let dropdown2Open = $state(false);
	let dropdown3Open = $state(false);
	let dropdown4Open = $state(false);

	// Track previous values to know which one changed
	let prevColor1 = $state(0);
	let prevColor2 = $state(1);
	let prevColor3 = $state(2);
	let prevColor4 = $state(3);

	// Function to find an unused color
	function findUnusedColor(excludeIndex) {
		const usedColors = [selectedColor1, selectedColor2, selectedColor3, selectedColor4];
		for (let i = 0; i < availableColors.length; i++) {
			if (i !== excludeIndex && !usedColors.includes(i)) {
				return i;
			}
		}
		// Fallback: return the first available
		return 0;
	}

	// Reactive effect to ensure no duplicate colors
	$effect(() => {
		const colors = [selectedColor1, selectedColor2, selectedColor3, selectedColor4];
		const prevColors = [prevColor1, prevColor2, prevColor3, prevColor4];
		
		// Find which color changed
		let changedIndex = -1;
		for (let i = 0; i < 4; i++) {
			if (colors[i] !== prevColors[i]) {
				changedIndex = i;
				break;
			}
		}
		
		if (changedIndex !== -1) {
			const newColorValue = colors[changedIndex];
			
			// Check if this color is already used elsewhere
			for (let i = 0; i < 4; i++) {
				if (i !== changedIndex && colors[i] === newColorValue) {
					// Found duplicate, assign unused color to the other element
					const unusedColor = findUnusedColor(newColorValue);
					
					if (i === 0) selectedColor1 = unusedColor;
					else if (i === 1) selectedColor2 = unusedColor;
					else if (i === 2) selectedColor3 = unusedColor;
					else if (i === 3) selectedColor4 = unusedColor;
				}
			}
			
			// Update previous values
			prevColor1 = selectedColor1;
			prevColor2 = selectedColor2;
			prevColor3 = selectedColor3;
			prevColor4 = selectedColor4;
		}
	});

	let color1 = $derived(availableColors[selectedColor1]);
	let color2 = $derived(availableColors[selectedColor2]);
	let color3 = $derived(availableColors[selectedColor3]);
	let color4 = $derived(availableColors[selectedColor4]);
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

<div class="sidebar-right">
	<Slider bind:value={breite2} min={30} max={100} step={0.1} label="Element size:" color={color1} />
	<Slider bind:value={breite1} min={30} max={100} step={0.1} label="Element size:" color={color4} />
	<Slider bind:value={rotation} min={-90} max={37} step={1} snapValues={[-26, 0]} label="Rotation: {rotation}°" />

	<div class="control-item">
		<label>Element 1 color:</label>
		<div class="custom-select">
			{#if !dropdown1Open}
				<button
					class="dropdown-trigger"
					style="background-color: {availableColors[selectedColor1]}; color: white;"
					onclick={() => dropdown1Open = true}
				>
					Color {selectedColor1 + 1}
				</button>
			{:else}
				<div class="color-options">
					{#each availableColors as color, index}
						<button
							class="color-option"
							class:selected={selectedColor1 === index}
							style="background-color: {color};"
							onclick={() => { selectedColor1 = index; dropdown1Open = false; }}
						>
							Color {index + 1}
						</button>
					{/each}
				</div>
			{/if}
		</div>
	</div>

	<div class="control-item">
		<label>Element 2 color:</label>
		<div class="custom-select">
			{#if !dropdown2Open}
				<button
					class="dropdown-trigger"
					style="background-color: {availableColors[selectedColor2]}; color: white;"
					onclick={() => dropdown2Open = true}
				>
					Color {selectedColor2 + 1}
				</button>
			{:else}
				<div class="color-options">
					{#each availableColors as color, index}
						<button
							class="color-option"
							class:selected={selectedColor2 === index}
							style="background-color: {color};"
							onclick={() => { selectedColor2 = index; dropdown2Open = false; }}
						>
							Color {index + 1}
						</button>
					{/each}
				</div>
			{/if}
		</div>
	</div>

	<div class="control-item">
		<label>Element 3 color:</label>
		<div class="custom-select">
			{#if !dropdown3Open}
				<button
					class="dropdown-trigger"
					style="background-color: {availableColors[selectedColor3]}; color: white;"
					onclick={() => dropdown3Open = true}
				>
					Color {selectedColor3 + 1}
				</button>
			{:else}
				<div class="color-options">
					{#each availableColors as color, index}
						<button
							class="color-option"
							class:selected={selectedColor3 === index}
							style="background-color: {color};"
							onclick={() => { selectedColor3 = index; dropdown3Open = false; }}
						>
							Color {index + 1}
						</button>
					{/each}
				</div>
			{/if}
		</div>
	</div>

	<div class="control-item">
		<label>Element 4 color:</label>
		<div class="custom-select">
			{#if !dropdown4Open}
				<button
					class="dropdown-trigger"
					style="background-color: {availableColors[selectedColor4]}; color: white;"
					onclick={() => dropdown4Open = true}
				>
					Color {selectedColor4 + 1}
				</button>
			{:else}
				<div class="color-options">
					{#each availableColors as color, index}
						<button
							class="color-option"
							class:selected={selectedColor4 === index}
							style="background-color: {color};"
							onclick={() => { selectedColor4 = index; dropdown4Open = false; }}
						>
							Color {index + 1}
						</button>
					{/each}
				</div>
			{/if}
		</div>
	</div>
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
		width: 100%;
		margin-bottom: 1rem;
	}

	.control-item input {
		width: 100%;
	}

	.control-item label {
		font-size: 0.75rem;
		margin-bottom: 0.3rem;
		color: #ccc;
	}

	.custom-select {
		width: 100%;
		/* border: 1px solid #777; */
		overflow: hidden;
		position: relative;
	}

	.dropdown-trigger {
		width: 100%;
		height: 28px;
		padding: 0 8px;
		padding-right: 24px;
		margin: 0;
		font-size: 0.75rem;
		border: none;
		border-radius: 0;
		color: white;
		cursor: pointer;
		text-align: left;
		font-variant-numeric: tabular-nums;
		transition: opacity 0.2s;
		display: block;
		position: relative;
	}

	.dropdown-trigger::after {
		content: '';
		position: absolute;
		right: 8px;
		top: 50%;
		transform: translateY(-50%);
		width: 0;
		height: 0;
		border-left: 4px solid transparent;
		border-right: 4px solid transparent;
		border-top: 5px solid white;
	}

	.dropdown-trigger:hover {
		opacity: 0.9;
	}

	.color-options {
		display: flex;
		flex-direction: column;
		gap: 0;
	}

	.color-option {
		width: 100%;
		height: 28px;
		padding: 0 8px;
		font-size: 0.75rem;
		border: none;
		border-radius: 0;
		margin: 0;
		color: white;
		cursor: pointer;
		text-align: left;
		font-variant-numeric: tabular-nums;
		transition: opacity 0.2s;
		display: block;
	}

	.color-option:hover {
		opacity: 0.8;
	}

	.color-option.selected {
		border-left: 3px solid white;
		border-right: 3px solid white;
		padding-left: 5px;
	}
</style>


