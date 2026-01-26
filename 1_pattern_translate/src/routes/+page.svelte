<script>
	import Header from '$lib/components/Header.svelte';
	import { slide } from 'svelte/transition';
	import PatternRect from '$lib/components/1_PatternRect.svelte';	
	// import PatternRectStruct from '$lib/components/2_PatternRectStruct.svelte';	
	// import PatternParalellogramm from '$lib/components/3_PatternParalellogramm.svelte';
	// import PatternParalellogramme2 from '$lib/components/4_PatternParalellogramme2.svelte';
	// import PatternParalellogramme2b from '$lib/components/4_PatternParalellogramme2b.svelte';
	import PatternParalellogramme2acolors from '$lib/components/4_PatternParalellogramme2acolors.svelte';
	import PatternParalellogramme2bcolors from '$lib/components/4_PatternParalellogramme2bcolors.svelte';
	import PatternParalellogramme2c from '$lib/components/4_PatternParalellogramme2c.svelte';
	import PatternParalellogramme2ccolors from '$lib/components/4_PatternParalellogramme2ccolors.svelte';
	import PatternParalellogramme3 from '$lib/components/4_PatternParalellogramme3.svelte';	
	// import PatternTriangle from '$lib/components/5_PatternTriangle.svelte';
	// import PatternParalellogramm3 from '$lib/components/6_PatternParalellogramm3.svelte';
	// import PatternCubes from '$lib/components/7_PatternCubes.svelte';

	let patterns = [
  {
    name: 'Squares in the Grid (size)',
    component: PatternParalellogramme2c,
    description:
      'A grid of squares divided into two triangles that can get an offset.'
  },
  {
    name: 'Squares in the Grid (colors1)',
    component: PatternParalellogramme3,
    description:
      'A grid of squares divided into two triangles that can get an offset and size.'
  },
  {
    name: 'Squares in the Grid (colorsB)',
    component: PatternParalellogramme2bcolors,
    description:
      'A grid of squares divided into two triangles that can get an offset and size.'
  },
  {
    name: 'Squares in the Grid (colorsA)',
    component: PatternParalellogramme2acolors,
    description:
      'A grid of squares divided into two triangles that can get an offset and size.'
  },
  {
    name: 'Squares in the Grid (colorsC)',
    component: PatternParalellogramme2ccolors,
    description:
      'A grid of squares divided into two triangles that can get an offset and size.'
  },
];

let selectedPattern = $state(0);
let SelectedPattern = $derived(patterns[selectedPattern].component);
</script>

<div class="app-container">
	<Header />
	<main class="app-main">
		<div class="sidebar-left">
			{#each patterns as pattern, index}
				<button
					class="sidebar-left-item"
					class:selected={selectedPattern === index}
					onclick={() => (selectedPattern = index)}
					>{pattern.name}
					{#if selectedPattern === index}
						<div transition:slide class="sidebar-left-description">{pattern.description}</div>
					{/if}
				</button>
			{/each}
		</div>

		<SelectedPattern />
	</main>
</div>

<style>
</style>
