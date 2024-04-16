<script>
  import * as d3 from "d3";
  import data from "../data/data.js";

  console.log(data);

  let width = 400;
  let height = 400;

  const xScale = d3.scaleLinear().domain([0, 100]).range([0, width]);
  const yScale = d3
    .scaleLinear()
    .domain([0, d3.max(data, (d) => d.hours)])
    .range([height, 0]);
</script>

<svg>
  {#each data as student}
    <circle cx={xScale(student.grade)} cy={yScale(student.hours)} r="4" />
  {/each}
</svg>
