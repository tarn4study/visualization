<script>
  /*import * as d3 from "d3";
  let width = 600;
  let height = 400;

  const marketPath =
    "https://raw.githubusercontent.com/tarn4study/visualization/master/src/data/bank_cap.csv";

  let marketData = {};

  d3.csv(marketPath).then((data) => {
    data.forEach((d) => {
      marketData[d.bank] = d.cap;
    });
  }); */

  import { onMount } from "svelte";
  import * as d3 from "d3";

  let width = 800;
  let height = 600;
  let data; // Placeholder for CSV data
  const scalingFactor = 1000;
  const centerX = width / 2;
  const centerY = height / 2;

  onMount(async () => {
    const csvData = await d3.csv(
      "https://raw.githubusercontent.com/tarn4study/visualization/master/src/data/bank_cap.csv"
    ); // Replace 'your_data.csv' with your actual file path
    data = csvData.map((d) => ({
      ...d,
      radius: (+d.cap / scalingFactor) * 0.2,
    })); // Assuming 'cap' is the numeric value for bubble size

    const svg = d3
      .select("#chart")
      .append("svg")
      .attr("width", width)
      .attr("height", height);

    const simulation = d3
      .forceSimulation(data)
      .force("x", d3.forceX().strength(0.08))
      .force("y", d3.forceY().strength(0.08))
      .force(
        "collide",
        d3
          .forceCollide()
          .radius((d) => d.radius + 1)
          .iterations(4)
      )
      .force("center", d3.forceCenter(centerX, centerY));

    const colorScale = d3.scaleOrdinal(d3.schemeCategory10);
    const tooltip = document.getElementById("tooltip");
    const circles = svg
      .append("g")
      .selectAll("circle")
      .data(data)
      .enter()
      .append("circle")
      .attr("r", (d) => d.radius)
      .attr("fill", (d) => colorScale(d.bank))
      .attr("stroke", "black")
      .attr("stroke-width", 1)
      .on("mouseover", function (event, d) {
        tooltip.style.display = "block"; // Show tooltip
        tooltip.innerHTML = `<b>Bank:</b> ${d.bank}`; // Update content
        const x = event.pageX + 10; // Adjust position based on mouse
        const y = event.pageY + 10;
        tooltip.style.left = `${x}px`;
        tooltip.style.top = `${y}px`;
      })
      .on("mouseout", () => {
        tooltip.style.display = "none";
      });

    simulation.on("tick", () => {
      circles.attr("cx", (d) => d.x).attr("cy", (d) => d.y);
    });

    d3.select(window).on("resize", () => {
      width = window.innerWidth;
      height = window.innerHeight;

      simulation.force("x", d3.forceX().strength(0.08));
      simulation.force("y", d3.forceY().strength(0.08));
      simulation.restart();

      svg.attr("width", width).attr("height", height);
    });
  });
</script>

<div id="chart"></div>
<div id="tooltip"></div>

<style>
  #tooltip {
    position: absolute;
    background-color: white;
    border: 1px solid black;
    padding: 5px;
    display: none;
  }
</style>
