<script>
  import LdrMap from "./LdrMap.svelte";
  import BubbleLoan from "./BubbleLoan.svelte";
  let selectedOption = "LdrMap";

  const handleOptionChange = (event) => {
    selectedOption = event.target.value;
  };
</script>

<select bind:value={selectedOption} on:change={handleOptionChange}>
  <option value="LdrMap">Show LdrMap</option>
  <option value="BubbleLoan">Show BubbleLoan</option>
</select>

{#if selectedOption === "BubbleLoan"}
  <BubbleLoan />
{:else}
  <LdrMap />
{/if}
