<!-- 
  - HTML und Svelte Code zeigen + Funktionsaufrufe
  - Zeige wie das erste kleine Pattern beginnt (außerhalb der Schleife)

  - 2 Rechtecke in der Schleife und entsprechend der Indizes x, y, width und height anpassen
  - Funktionen müssen sie schreiben 
  - Kleiner Tipp mit data-x um zu sehen welche Indizes die Rects haben
-->

<script>
	const squareCount = 20;
	const squareSize = 1000 / squareCount;
	const offset = squareSize / 4; // 45-Grad-Versatz

	let factor = 1;
	let scaleFactor = 1;
	let heightFactor = 1;
	let rotationAngle = 0;

	// function calculateSizeCords1(xi, yi) {

	// }

	// function calculateSizeCords2(xi, yi) {

	// }
</script>

<div id="control">
	<div class="control-item">
		<input type="range" min="1" max="2.5" step="0.05" bind:value={factor}/>
		<label>Abstand: {factor.toFixed(2)}</label>
	</div>
	<div class="control-item">
		<input type="range" min="0.5" max="2" step="0.05" bind:value={scaleFactor}/>
		<label>Breite: {scaleFactor.toFixed(2)}</label>
	</div>
	<div class="control-item">
		<input type="range" min="0.5" max="2" step="0.05" bind:value={heightFactor}/>
		<label>Höhe: {heightFactor.toFixed(2)}</label>
	</div>
	<div class="control-item">
		<input type="range" min="0" max="360" step="1" bind:value={rotationAngle}/>
		<label>Rotation: {rotationAngle}°</label>
	</div>
</div>


<div class="svg-container">
	<svg viewBox="0 0 1000 1000" class="svg-canvas">

        {#each Array(10) as _, j}	
            <g transform="translate({j*50*factor} {j*150*factor})">
                {#each Array(10) as _, i}
                    <polygon transform="translate({-400+i*150*factor} {0-i*50*factor}) rotate({rotationAngle}) scale({scaleFactor}, {heightFactor})" points="0 0 100 0 150 50 50 50" fill="hotpink" />
                    <polygon transform="translate({-200+i*150*factor} {0-i*50*factor}) rotate({90+rotationAngle}) scale({scaleFactor}, {heightFactor})" points="0 0 100 0 150 50 50 50" fill="dodgerblue" />
                    <rect transform="translate({-350+i*150*factor} {50-i*50*factor}) rotate({rotationAngle}) scale({scaleFactor}, {heightFactor})" width="100" height="100" fill="#ff0" />
                    <polygon transform="translate({-400+i*150*factor} {0-i*50*factor}) rotate({rotationAngle}) scale({scaleFactor}, {heightFactor})" points="100 0 150 -50 200 0 150 50" fill="#0ff" />
                {/each}
            </g>    
        {/each}


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