<script>
	import Header from '$lib/components/Header.svelte';
	import PatternRect from '$lib/components/1_PatternRect.svelte';	
	// import PatternRectStruct from '$lib/components/2_PatternRectStruct.svelte';	
	// import PatternParalellogramm from '$lib/components/3_PatternParalellogramm.svelte';
	// import PatternParalellogramme2 from '$lib/components/4_PatternParalellogramme2.svelte';
	// import PatternParalellogramme2b from '$lib/components/4_PatternParalellogramme2b.svelte';
	import PatternParalellogramme2acolors from '$lib/components/4_PatternParalellogramme2acolors.svelte';
	import PatternParalellogramme2bcolors from '$lib/components/4_PatternParalellogramme2bcolors.svelte';
	import PatternParalellogramme2c from '$lib/components/4_PatternParalellogramme2c.svelte';
	// import PatternTriangle from '$lib/components/5_PatternTriangle.svelte';
	// import PatternParalellogramm3 from '$lib/components/6_PatternParalellogramm3.svelte';
	// import PatternCubes from '$lib/components/7_PatternCubes.svelte';

	// Load pattern from localStorage or default to 'PatternParalellogramme2bcolors'
	let selectedPattern = $state(
		typeof window !== 'undefined' 
			? (localStorage.getItem('selectedPattern') || 'PatternParalellogramme2bcolors')
			: 'PatternParalellogramme2bcolors'
	);

	const patterns = [
		{ id: 'PatternRect', name: '1. Pattern Rect' },
		// { id: 'PatternRectStruct', name: '2. Pattern Rect Struct (Parquet)' },
		// { id: 'PatternParalellogramm', name: '3. Pattern Parallelogramm' },
		// { id: 'PatternParalellogramme2', name: '4. Pattern Parallelogramme 2' },
		// { id: 'PatternParalellogramme2b', name: '4b. Pattern Parallelogramme 2b' },
		{ id: 'PatternParalellogramme2acolors', name: '4a. Pattern Parallelogramme 2a Colors' },
		{ id: 'PatternParalellogramme2bcolors', name: '4b. Pattern Parallelogramme 2b Colors' },
		{ id: 'PatternParalellogramme2c', name: '4c. Pattern Parallelogramme 2c' },
		// { id: 'PatternTriangle', name: '5. Pattern Triangle' },
		// { id: 'PatternParalellogramm3', name: '6. Pattern Parallelogramm 3' },
		// { id: 'PatternCubes', name: '7. Pattern Cubes' },
	];

	$effect(() => {
		console.log('Selected pattern:', selectedPattern);
		// Save to localStorage whenever it changes
		if (typeof window !== 'undefined') {
			localStorage.setItem('selectedPattern', selectedPattern);
		}
	});
</script>

<div class="app-container">
	<Header />
	
	<div class="pattern-selector">
		<label for="pattern-select">Muster auswählen:</label>
		<select id="pattern-select" bind:value={selectedPattern}>
			{#each patterns as pattern}
				<option value={pattern.id}>{pattern.name}</option>
			{/each}
		</select>
	</div>

	<main class="app-main">
		{#if selectedPattern === 'PatternRect'}
			<PatternRect />
		<!-- {:else if selectedPattern === 'PatternRectStruct'}
			<PatternRectStruct />
		{:else if selectedPattern === 'PatternParalellogramm'}
			<PatternParalellogramm />
		<!-- {:else if selectedPattern === 'PatternParalellogramme2'}
			<PatternParalellogramme2 /> -->
		<!-- {:else if selectedPattern === 'PatternParalellogramme2b'}
			<PatternParalellogramme2b /> -->
		{:else if selectedPattern === 'PatternParalellogramme2acolors'}
			<PatternParalellogramme2acolors />
		{:else if selectedPattern === 'PatternParalellogramme2bcolors'}
			<PatternParalellogramme2bcolors />
		{:else if selectedPattern === 'PatternParalellogramme2c'}
			<PatternParalellogramme2c />
		<!-- {:else if selectedPattern === 'PatternTriangle'}
			<PatternTriangle />
		{:else if selectedPattern === 'PatternParalellogramm3'}
			<PatternParalellogramm3 />
		{:else if selectedPattern === 'PatternCubes'}
			<!-- <PatternCubes /> -->
		{/if}
	</main>
</div>

<style>
	.pattern-selector {
		padding: 20px;
		display: flex;
		align-items: center;
		gap: 10px;
		background: #f5f5f5;
		border-bottom: 1px solid #ddd;
	}

	.pattern-selector label {
		font-weight: 500;
		font-size: 14px;
	}

	.pattern-selector select {
		padding: 8px 12px;
		border: 1px solid #ccc;
		border-radius: 4px;
		font-size: 14px;
		background: white;
		cursor: pointer;
		min-width: 300px;
	}

	.pattern-selector select:hover {
		border-color: #999;
	}

	.pattern-selector select:focus {
		outline: none;
		border-color: #4CAF50;
		box-shadow: 0 0 0 2px rgba(76, 175, 80, 0.2);
	}
</style>
