<!-- 
  - Komplett mit Komponenten und Code zeigen 
-->

<script>
	const squareSize = 200;
</script>

<div class="svg-container">
	<svg viewBox="0 0 1000 1000" class="svg-canvas" shape-rendering="crispEdges">
		{#each Array(squareCount) as _, yi}
			{#each Array(squareCount) as _, xi};
			{/each}
		{/each}
		<polygon points="" fill="hotpink" />
	</svg>
</div>
