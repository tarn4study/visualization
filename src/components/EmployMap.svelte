<script>
  import * as d3 from "d3";
  export let width = 800;
  export let height = 600;

  const geojsonPath =
    "https://raw.githubusercontent.com/tarn4study/visualization/master/src/data/thaimap.json";

  const employPath =
    "https://raw.githubusercontent.com/tarn4study/visualization/master/src/data/employ2565.csv";

  let geojson;
  d3.json(geojsonPath).then((data) => (geojson = data));

  let employdata;
  const employDict = {};
  d3.csv(employPath).then((data) => {
    employdata = data;

    employdata.forEach((dataPoint) => {
      employDict[dataPoint.province] = dataPoint.log;
    });
  });

  $: console.log(employdata);

  $: projection = d3.geoMercator().fitSize([width, height], geojson);

  $: pathGenerator = d3.geoPath(projection);

  let colorScale = d3
    .scaleLinear()
    .domain([9.5, 15.5])
    .range([d3.rgb(192, 200, 252), d3.rgb(0, 34, 252)]);

  let provinces = [];
  $: if (geojson)
    provinces = geojson.features.map((feature) => {
      return {
        ...feature,
        path: pathGenerator(feature),
      };
    });
  $: console.log(`name: ${name}`);
  let name = null;
  // 125 150 400 300 scale 1.5
</script>

<div style="display: flex; justify-content: center;">
  <h3>จำนวนการจ้างงานใน SME</h3>
</div>
<div id="employ-container">
  <svg id="employ" {width} {height}>
    {#each provinces as { path, properties }}
      <path
        d={path}
        fill={colorScale(employDict[properties.name])}
        class:active={name === properties.name}
        on:mouseenter={() => (name = properties.name)}
        role="presentation"
      />
    {/each}
  </svg>
  <svg id="legend-container-employ">
    <g class="legend" transform={`translate(0, 0)`}>
      <g transform="translate(20,0)">
        <rect
          class="legend-color"
          width="200"
          height="18"
          style="fill: url(#linearGradientEmploy)"
        />
      </g>
      <linearGradient id="linearGradientEmploy">
        <stop offset="0%" stop-color={d3.rgb(192, 200, 252)} />
        <stop offset="100%" stop-color={d3.rgb(0, 34, 252)} />
      </linearGradient>
      <g class="legendLabels" font-family="sans-serif" font-size="10">
        <svg>
          <g transform="translate(10,0)">
            <text x="10" y="30" dx=".3em" text-anchor="end"> 9.5 </text>
            <text x="212" y="30" dx=".3em" text-anchor="end"> 15.5 </text>
          </g>
          <g transform="translate(10,0)">
            <text x="170" y="40" dx=".3em" text-anchor="end"
              >log ของจำนวนการจ้างงานใน SME
            </text>
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
  #employ {
    z-index: 0;
    position: relative;
  }
  rect.legend-color {
    stroke: black;
    stroke-width: 0.5px;
  }
  #legend-container-employ {
    z-index: 1;
    position: relative;
  }
  #employ-container {
    display: flex;
    flex-direction: column;
    margin-bottom: 0%;
  }
</style>
