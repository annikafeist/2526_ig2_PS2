<script>

	const squareCount = 20;
    const squareSize = 80;
	const x = squareSize / 2;
	const deg = 30;
	const rad = (Math.PI / 180) * deg;

	const y = Math.tan(rad) * x;
    const hypo = x / Math.cos(rad);

    function getTranslate(xi, yi) {
        let x = xi * squareSize;
        const y1 = yi * (y + hypo);

        if (yi % 2) {
            x += squareSize / -2;
        }

        return `translate(${x} ${y1})`
	}
    
</script>

<div class="svg-container">
	<svg viewBox="0 0 1000 1000" class="svg-canvas" shape-rendering="crispEdges">
		{#each Array(squareCount) as _, yi}
			{#each Array(squareCount) as _, xi}
				<g transform={getTranslate(xi, yi)}>
			        <polygon points="0 0, {x} {y}, {x * 2} 0, {x} {-y}" fill="Slategray" />
			        <polygon transform="rotate(120 {x} {y})" points="0 0, {x} {y}, {x * 2} 0, {x} {-y}" fill="Lightsteelblue" />
			        <polygon transform="rotate(240 {x} {y})" points="0 0, {x} {y}, {x * 2} 0, {x} {-y}" fill="Darkslategray" />
		        </g>
			{/each}
		{/each}
	</svg>
</div>