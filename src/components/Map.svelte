<script>
  import * as d3 from "d3";
  let width = 1380;
  let height = 600;

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
  // 300 200 50 200 scale 1.5

  const svg = document.getElementById("ldr");
</script>

<div id="zoom-container">
  <div>
    <svg id="ldr" {width} {height} viewBox="500 275 300 25">
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
    <svg {width}>
      <g class="legend" transform={`translate(575, 0)`}>
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
              {#each d3.scaleLinear().domain([0, 200]).ticks(2) as tick}
                <text x={tick} y="30" dx=".3em" text-anchor="end">
                  {tick}
                </text>
              {/each}
            </g>
          </svg>
        </g>
      </g>
    </svg>
    <p>แผนที่แสดงอัตราส่วนสินเชื่อต่อเงินฝาก</p>
  </div>
</div>

<style>
  #zoom-container {
    display: flex;
    flex-direction: column;
    justify-content: center;
  }
  path {
    stroke: none;
    opacity: 1;
  }
  path.active {
    stroke: black;
    stroke-width: 0.5px;
  }
  #ldr {
    transform: scale(1);
  }
  p {
    margin-top: -8%;
    margin-bottom: 7%;
    text-align: center;
  }
</style>
