<script>
	const squareCount = 20;
	const squareSize = 1000 / squareCount;

	let offset = $state(0);
</script>

<div id="control">
	<input type="range" min="0" max="100" bind:value={offset} />
	<label>{offset}</label>
</div>

<div class="svg-container">
	<svg viewBox="0 0 1000 1000" class="svg-canvas" shape-rendering="crispEdges">
		{#each Array(squareCount) as _, yi}
			{#each Array(squareCount) as _, xi}
				<polygon
					points="0 0 50 0 0 50"
					fill="lightpink"
					transform="translate({xi * (squareSize + offset)}, {yi * (squareSize + offset)})"
				/>
				<polygon
					points="50 0 0 50 50 50"
					fill="maroon"
					transform="translate({xi * (squareSize + offset)}, {yi * (squareSize + offset)})"
				/>
			{/each}
		{/each}
	</svg>
</div>

<style>
	#control {
		display: flex;
		width: 150px;
		justify-content: left;
		align-items: center;
	}
	input {
		width: 50%;
	}
</style>
