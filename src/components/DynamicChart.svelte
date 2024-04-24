<script>
  import LdrMap from "./LdrMap.svelte";
  import BubbleLoan from "./BubbleLoan.svelte";
  let show = false;
</script>

<input type="checkbox" bind:checked={show} />

{#if show}
  <BubbleLoan />
{:else}
  <LdrMap />
{/if}
