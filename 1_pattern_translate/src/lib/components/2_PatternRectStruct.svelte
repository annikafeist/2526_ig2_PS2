<script>
	const squareCount = 20;
	const squareSize = 1000 / squareCount;

	function calculateSizeCords1(xi, yi) {

	}

	function calculateSizeCords2(xi, yi) {

	}
</script>

<div class="svg-container">
	<svg viewBox="0 0 1000 1000" class="svg-canvas">
	
		{#each Array(20) as _, j}
			{#each Array(10) as _, i}
				{#if j % 2 === 0}
					<!-- Gerade Reihen: horizontal, dann vertikal -->
					<rect transform="translate({i * squareSize * 2} {j * squareSize})" width={squareSize} height={squareSize / 2} fill="hotpink" />

					<rect
						transform="translate({i * squareSize * 2} {j * squareSize + squareSize / 2})"
						width={squareSize}
						height={squareSize / 2}
						fill="maroon"
					/>

					<rect
						transform="translate({i * squareSize * 2 + squareSize} {j * squareSize})"
						width={squareSize / 2}
						height={squareSize}
						fill="hotpink"
					/>
					<rect
						transform="translate({i * squareSize * 2 + squareSize + squareSize / 2} {j * squareSize})"
						width={squareSize / 2}
						height={squareSize}
						fill="maroon"
					/>
				{:else}
					<!-- Ungerade Reihen: vertikal, dann horizontal -->
					<rect
						transform="translate({i * squareSize * 2} {j * squareSize})"
						width={squareSize / 2}
						height={squareSize}
						fill="hotpink"
					/>
					<rect
						transform="translate({i * squareSize * 2 + squareSize / 2} {j * squareSize})"
						width={squareSize / 2}
						height={squareSize}
						fill="maroon"
					/>

					<rect transform="translate({i * squareSize * 2 + squareSize} {j * squareSize})" width={squareSize} height={squareSize / 2} fill="hotpink" />

					<rect
						transform="translate({i * squareSize * 2 + squareSize} {j * squareSize + squareSize / 2})"
						width={squareSize}
						height={squareSize / 2}
						fill="maroon"
					/>
				{/if}
			{/each}
		{/each}
	</svg>
</div>
