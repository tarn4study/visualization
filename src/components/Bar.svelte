<script>
	import { onMount } from 'svelte';
	let chartData;
  
	async function fetchData() {
	  const response = await fetch('https://your-data-source.com/data.json');
	  chartData = await response.json();
	}
  
	onMount(fetchData);
  </script>
  

  <div style="min-height:550px"><script type="text/javascript" defer src="https://datawrapper.dwcdn.net/2Ucxe/embed.js?v=8" charset="utf-8"></script><noscript><img src="https://datawrapper.dwcdn.net/2Ucxe/full.png" alt="" /></noscript></div>
  
  {#if chartData}
	<div style="width: 100%; height: 401px;">
	  <svg width="100%" height="401px">
		</svg>
	</div>
  {/if}
  