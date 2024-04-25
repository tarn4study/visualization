<script>
  import * as d3 from "d3";

  export let width = 800;
  export let height = 600;

  const geojsonPath =
    "https://raw.githubusercontent.com/tarn4study/visualization/master/src/data/thailandWithName.json";

  const ldrPath =
    "https://raw.githubusercontent.com/tarn4study/visualization/master/src/data/ldr-money.csv";

  const path =
    "https://raw.githubusercontent.com/tarn4study/visualization/master/src/data/LDR.csv";

  let geojson;
  d3.json(geojsonPath).then((data) => (geojson = data));

  let ldrdata;
  let ldrratiodata;
  const ldrDict = {};
  d3.csv(path).then((data) => {
    ldrratiodata = data;

    ldrratiodata.forEach((dataPoint) => {
      ldrDict[dataPoint.province] = dataPoint.LDR;
    });
  });
  const ldrDictLoan = {};
  const ldrDictDep = {};
  d3.csv(ldrPath).then((data) => {
    ldrdata = data;

    ldrdata.forEach((dataPoint) => {
      ldrDictLoan[dataPoint.province] = dataPoint.loan;
      ldrDictDep[dataPoint.province] = dataPoint.deposit;
    });
  });

  $: console.log(`ldrDict ${ldrDictLoan["Bangkok"]}`);

  $: projection = d3.geoMercator().fitSize([width, height], geojson);

  $: pathGenerator = d3.geoPath(projection);

  let colorScale = d3
    .scaleLinear()
    .domain([100, 200])
    .range([d3.rgb(251, 215, 43), d3.rgb(249, 72, 74)]);

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
<div id="ldr-avg-container">
  <svg id="ldr-f-map" {width} {height}>
    {#each provinces as { path, properties }}
      <path
        d={path}
        fill={ldrDictLoan[properties.name] > ldrDictDep[properties.name] &&
        ldrDictDep[properties.name] > 221159.92
          ? colorScale(ldrDict[properties.name])
          : d3.rgb(211, 211, 211)}
        class:active={name === properties.name}
        on:mouseenter={() => (name = properties.name)}
        role="presentation"
      />
    {/each}
  </svg>
  <svg id="legend-container">
    <g class="legend" transform={`translate(0, 0)`}>
      <g transform="translate(10,0)">
        <rect
          width="200"
          height="18"
          style="fill: url(#linearGradient-HighLoanMap)"
        />
      </g>
      <linearGradient id="linearGradient-HighLoanMap">
        <stop offset="0%" stop-color={d3.rgb(251, 215, 43)} />
        <stop offset="100%" stop-color={d3.rgb(249, 72, 74)} />
      </linearGradient>
      <g class="legendLabels" font-family="sans-serif" font-size="10">
        <svg>
          <g transform="translate(10,0)">
            <text x="10" y="30" dx=".3em" text-anchor="end"> 100 </text>
            <text x="210" y="30" dx=".3em" text-anchor="end"> 200 </text>
          </g>
        </svg>
      </g>
      <g transform="translate(10,40)">
        <rect width="18" height="18" style="fill: rgb(211, 211, 211)" />
      </g>
      <g class="legendLabels" font-family="sans-serif" font-size="10">
        <svg>
          <g transform="translate(130,25)">
            <text x="10" y="30" dx=".3em" text-anchor="end">
              deoposit more than loan
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
  #ldr-avg-container {
    display: flex;
    flex-direction: column;
  }
</style>
