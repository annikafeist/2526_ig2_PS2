<!-- 
  - HTML und Svelte Code zeigen + Funktionsaufrufe
  - Zeige wie das erste kleine Pattern beginnt (außerhalb der Schleife)

  - 2 Rechtecke in der Schleife und entsprechend der Indizes x, y, width und height anpassen
  - Funktionen müssen sie schreiben 
  - Kleiner Tipp mit data-x um zu sehen welche Indizes die Rects haben
-->

<script>
	import Slider from '$lib/ui/Slider.svelte';

	const squareCount = 20;
	const squareSize = 1000 / squareCount;
	const offset = squareSize / 4; // 45-Grad-Versatz

	let breite1 = 50;
	let breite2 = 50;

	// function calculateSizeCords1(xi, yi) {

	// }

	// function calculateSizeCords2(xi, yi) {

	// }
</script>

<div id="control">
	<Slider bind:value={breite1} min={35} max={95} step={0.1} label="Breite1: {breite1.toFixed(2)}" />
	<Slider bind:value={breite2} min={35} max={95} step={0.1} label="Breite2: {breite2.toFixed(2)}" />
</div>

<div class="svg-container">
	<svg viewBox="-500 -500 1000 1000" class="svg-canvas">
		{#each Array(20) as _, j}
			<g transform="translate({(j-10) * breite2} {(j-10) * (breite1 + breite2)} )">
				{#each Array(20) as _, i}
					<g transform="translate({(i-10) * (breite1 + breite2)} {(i-10) * -breite2})">
						<rect transform="translate(0 0)" width={breite1} height={breite1} fill="#ff0" />
						<polygon
							transform="translate(0 {breite1 + breite2})"
							points="0 {-breite2} {breite2} 0 0 {breite2} {-breite2} 0"
							fill=maroon/>
						<polygon 
							transform="translate(0)"
							points="0 {breite1}, {breite1} {breite1}, {breite1+breite2} {breite1+breite2}, {breite2} {breite1+breite2}"
							fill="#f0f" />
						<polygon 
							transform="translate(0)"
							points="{breite1} 0, {breite1} {breite1}, {breite1+breite2} {breite1-breite2}, {breite1+breite2} {-breite2}"
							fill="#fa0" />
					</g>

					<!-- <polygon transform="translate({(breite1+breite2)*i+breite1+breite2} {(breite1+breite2)*i+breite1})" points="0 {-breite2} {breite2} 0 0 {breite2} {-breite2} 0" fill="#0ff" /> -->

					<!-- <polygon transform="translate({-400+i*150} {0-i*50}) rotate({0}) " points="0 0 100 0 150 50 50 50" fill="hotpink" />
                    <polygon transform="translate({-200+i*150} {0-i*50}) rotate({90}) " points="0 0 100 0 150 50 50 50" fill="dodgerblue" />
                    <rect transform="translate({-350+i*150} {50-i*50})" width={breite1} height={breite1} fill="#ff0" />
                    <polygon transform="translate({-400+i*150} {0-i*50})" points="0 {-breite2} {breite2} 0 0 {breite2} {-breite2} 0" fill="#0ff" /> -->
				{/each}
			</g>
		{/each}

		<!-- <rect transform="translate({200} {200})" width={breite1} height={breite1} fill="#ff0" />
		<polygon transform="translate({200} {200+breite1+breite2})" points="0 {-breite2} {breite2} 0 0 {breite2} {-breite2} 0" fill="#0ff" />

		<rect transform="translate({200+breite1+breite2} {200-breite2})" width={breite1} height={breite1} fill="#ff0" />
		<polygon transform="translate({200+breite1+breite2} {200+breite1+breite2-breite2})" points="0 {-breite2} {breite2} 0 0 {breite2} {-breite2} 0" fill="#0ff" /> -->

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
</style>
