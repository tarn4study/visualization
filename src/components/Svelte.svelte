<script>
  let name;
</script>

<div>
  <h1>Hello from {name || "Svelte"}!</h1>
  <input placeholder="enter name" bind:value={name} />
</div>
