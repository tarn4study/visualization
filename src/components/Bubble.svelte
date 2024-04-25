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

    const backgroundCircle = {
      cx: width / 2 + 15,
      cy: height / 2,
      radius: Math.max(...data.map((d) => d.radius)) * 2.75,
      fill: "lightgray",
      opacity: 0.2,
    };

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

    svg
      .append("circle")
      .attr("r", backgroundCircle.radius)
      .attr("cx", backgroundCircle.cx)
      .attr("cy", backgroundCircle.cy)
      .attr("fill", backgroundCircle.fill)
      .attr("opacity", backgroundCircle.opacity);

    function colorScale(bankname) {
      if (bankname === "bay") {
        return d3.color("#ffdc7d").rgb();
      } else if (bankname === "bbl") {
        return d3.color("#679ecb").rgb();
      } else if (bankname === "cimbt") {
        return d3.color("#ff7276").rgb();
      } else if (bankname === "credit") {
        return d3.color("#6690d3").rgb();
      } else if (bankname === "kbank") {
        return d3.color("#6ebc85").rgb();
      } else if (bankname === "kkp") {
        return d3.color("#9995b0").rgb();
      } else if (bankname === "ktb") {
        return d3.color("#6ac9f1").rgb();
      } else if (bankname === "lhfg") {
        return d3.color("#d9d266").rgb();
      } else if (bankname === "scb") {
        return d3.color("#957fb3").rgb();
      } else if (bankname === "tcap") {
        return d3.color("#f8aa7b").rgb();
      } else if (bankname === "tisco") {
        return d3.color("#679ecb").rgb();
      } else if (bankname === "ttb") {
        return d3.color("#709af4").rgb();
      }
    }
    const tooltip = document.getElementById("tooltip");
    const circles = svg
      .append("g")
      .selectAll("circle")
      .data(data)
      .enter()
      .append("circle")
      .attr("r", (d) => d.radius)
      .attr("fill", (d) => colorScale(d.bank))
      .attr("stroke-width", 0)
      .on("mouseover", function (event, d) {
        tooltip.style.display = "block";
        const highlightWord = "พระบาทสมเด็จพระวชิรเกล้าเจ้าอยู่หัว";
        let highlightedText = `<b>ผู้ถือหุ้นรายใหญ่:</b> `;
        if (d.share.includes(highlightWord)) {
          const parts = d.share.split(new RegExp(highlightWord, "gis"));
          console.log(parts);
          highlightedText += parts
            .map((part) => {
              if (part === highlightWord) {
                return `<span style="color: yellow; font-weight: bold;">${part}</span>`;
              } else {
                return part;
              }
            })
            .join("");
        } else {
          highlightedText += d.share;
        }

        tooltip.innerHTML = highlightedText;
        const x = event.pageX + 10;
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

<h3>
  ปัญหาเรื่องความเหลื่อมล้ำในการเข้าถึงแหล่งเงินทุน
  เราเชื่อว่าส่วนหนึ่งเป็นเพราะมีธนาคารน้อยเกินไป
</h3>
<h3>
  หากมีจำนวนธนาคารมากกว่านี้ การแข่งขันในการปล่อยกู้ก็จะสูงขึ้นกว่าปัจจุบัน
  ส่งผลให้ต่างจังหวัดเข้าถึงแหล่งทุนได้มากขึ้น
</h3>
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
  h3 {
    text-align: center;
    font-size: 3vh;
  }
  #chart {
    margin-left: 90px;
  }
</style>
