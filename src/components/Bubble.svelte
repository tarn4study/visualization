<script>
  import * as d3 from "d3";
  import data from "../data/data";
  let width = 800;
  let height = 600;

  const marketPath =
    "https://raw.githubusercontent.com/tarn4study/visualization/master/src/data/bank_cap.csv";

  let marketData = {};

  d3.csv(marketPath).then((data) => {
    data.forEach((d) => {
      marketData[d.bank] = d.cap;
    });
  });
</script>

<svg></svg>
