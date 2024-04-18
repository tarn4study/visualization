<script>
  import * as d3 from "d3";
  let width = 800;
  let height = 600;

  const geojsonPath =
    "https://raw.githubusercontent.com/apisit/thailand.json/master/thailandWithName.json";

  const ldrPath =
    "https://raw.githubusercontent.com/tarn4study/visualization/master/src/data/LDR.csv";

  let geojson;
  d3.json(geojsonPath).then((data) => (geojson = data));

  let ldrdata;
  d3.csv(ldrPath).then((data) => {
    ldrdata = data;
  });

  $: console.log(ldrdata);

  $: projection = d3.geoMercator().fitSize([width, height], geojson);

  $: pathGenerator = d3.geoPath(projection);

  let provinces = [];
  $: if (geojson)
    provinces = geojson.features.map((feature) => {
      return {
        ...feature,
        path: pathGenerator(feature),
      };
    });
  $: console.log(provinces);
  let name = null;
  let ldrvalue = null;
</script>

<p>{name}</p>
<p>{ldrvalue}</p>
<div class="map">
  <svg {width} {height}>
    {#each provinces as { path, properties }}
      <path
        d={path}
        class:active={name === properties.name}
        on:mouseenter={() => (name = properties.name)}
        role="presentation"
      />
    {/each}
  </svg>
</div>

<style>
  path {
    fill: green;
    stroke: none;
    opacity: 0.5;
    transition: opacity 0.4s ease-in-out;
  }
  path.active {
    opacity: 1;
  }
  div.map {
    position: absolute;
    right: 0vh;
  }
</style>
