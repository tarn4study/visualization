<script>
  import * as d3 from "d3";
  export let width = 800;
  export let height = 600;

  const geojsonPath =
    "https://raw.githubusercontent.com/tarn4study/visualization/master/src/data/thailandWithName.json";

  const ldrPath =
    "https://raw.githubusercontent.com/tarn4study/visualization/master/src/data/LDR.csv";

  let geojson;
  d3.json(geojsonPath).then((data) => (geojson = data));

  let ldrdata;
  const ldrDict = {};
  d3.csv(ldrPath).then((data) => {
    ldrdata = data;

    ldrdata.forEach((dataPoint) => {
      ldrDict[dataPoint.province] = dataPoint.LDR;
    });
  });

  $: console.log(ldrdata);

  $: projection = d3.geoMercator().fitSize([width, height], geojson);

  $: pathGenerator = d3.geoPath(projection);

  let colorScale = d3
    .scaleLinear()
    .domain([0, 100, 200])
    .range([
      d3.interpolateRdBu(0),
      d3.interpolateRdBu(0.5),
      d3.interpolateRdBu(1),
    ]);

  $: console.log(`color: ${d3.interpolateRdBu(0)}`);

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
  // 125 150 400 300 scale 1.5
</script>

<div><h3>อัตราส่วนสินเชื่อต่อเงินฝาก</h3></div>
<div id="ldr-container">
  <svg id="ldr-f-map" {width} {height}>
    {#each provinces as { path, properties }}
      <path
        d={path}
        fill={colorScale(ldrDict[properties.name])}
        class:active={name === properties.name}
        on:mouseenter={() => (name = properties.name)}
        role="presentation"
      />
    {/each}
  </svg>
  <svg id="legend-container">
    <g class="legend" transform={`translate(0, 0)`}>
      <g transform="translate(10,0)">
        <rect width="200" height="18" style="fill: url(#linearGradient)" />
      </g>
      <linearGradient id="linearGradient">
        <stop offset="0%" stop-color={d3.interpolateRdBu(0)} />
        <stop offset="50%" stop-color={d3.interpolateRdBu(0.5)} />
        <stop offset="100%" stop-color={d3.interpolateRdBu(1)} />
      </linearGradient>
      <g class="legendLabels" font-family="sans-serif" font-size="10">
        <svg>
          <g transform="translate(10,0)">
            <text x="0" y="30" dx=".3em" text-anchor="end"> 0 </text>
            <text x="105" y="30" dx=".3em" text-anchor="end"> 100 </text>
            <text x="200" y="30" dx=".3em" text-anchor="end"> 200 </text>
          </g>
        </svg>
      </g>
    </g>
  </svg>
</div>

<style>
  path {
    stroke: none;
    opacity: 1;
  }
  path.active {
    stroke: black;
    stroke-width: 0.5px;
  }
  #ldr-container {
    display: flex;
    flex-direction: column;
  }
</style>
