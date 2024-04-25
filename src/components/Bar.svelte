<script>
  import { onMount } from "svelte";
  let chartData;

  async function fetchData() {
    const response = await fetch("https://your-data-source.com/data.json");
    chartData = await response.json();
  }

  onMount(fetchData);
</script>

<div style="min-height:100px">
  <img src="/bar.png" alt="" />
</div>

{#if chartData}
  <div style="width: 100%; height: 401px;">
    <svg width="100%" height="401px"> </svg>
  </div>
{/if}
