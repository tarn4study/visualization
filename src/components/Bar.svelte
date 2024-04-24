<script>
	import { scaleLinear } from 'd3-scale';

	const points = [
		{ year: 2550, ratio: 103.19 },
		{ year: 2566, ratio: 127.97 },
		{ year: 2550, ratio: 93.01 },
		{ year: 2566, ratio: 107.04 }
	];

	const xTicks = ['Bangkok','Bangkok','Thailand','Thailand'];
	const yTicks = [20,40,60,80,100,120];
	const padding = { top: 20, right: 15, bottom: 20, left: 25 };

	let width = 100;
	let height = 900;

	$: xScale = scaleLinear()
		.domain([0, xTicks.length])
		.range([padding.left, width - padding.right]);

	$: yScale = scaleLinear()
		.domain([0, Math.max.apply(null, yTicks)])
		.range([height - padding.bottom, padding.top]);

	$: innerWidth = width - (padding.left + padding.right);
	$: barWidth = innerWidth / xTicks.length;
</script>

<h2>อัตราส่วนเงินกู้ต่อเงินฝาก</h2>

<div class="chart" bind:clientWidth={width} bind:clientHeight={height}>
	<svg>
		<g class="axis y-axis">
			{#each yTicks as tick}
				<g class="tick tick-{tick}" transform="translate(0, {yScale(tick)})">
					<line x2="100%" />
					<text y="-4">{tick} </text>
				</g>
			{/each}
		</g>

		<g class="axis x-axis">
			{#each points as point, i}
				<g class="tick" transform="translate({xScale(i)},{height})">
					<text x={barWidth / 2} y="-4">{width > 380 ? point.year : point.year}</text>
				</g>
			{/each}
		</g>

		<g class="bars">
			{#each points as point, i}
				<rect
					x={xScale(i) + 2}
					y={yScale(point.ratio)}
					width={barWidth - 4}
					height={yScale(0) - yScale(point.ratio)}
				/>
			{/each}
		</g>
	</svg>
</div>

<style>
	h2 {
		text-align: center;
	}

	.chart {
		width: 100%;
		max-width: 500px;
		margin: 0 auto;
	}

	svg {
		position: relative;
		width: 100%;
		height: 200px;
	}

	.tick {
		font-family: Helvetica, Arial;
		font-size: 0.725em;
		font-weight: 200;
	}

	.tick line {
		stroke: #e2e2e2;
		stroke-dasharray: 2;
	}

	.tick text {
		fill: #ccc;
		text-anchor: start;
	}

	.tick.tick-0 line {
		stroke-dasharray: 0;
	}

	.x-axis .tick text {
		text-anchor: middle;
	}

	.bars rect {
		fill: rgb(0, 68, 255);
		stroke: none;
		opacity: 0.65;
	}
</style>
