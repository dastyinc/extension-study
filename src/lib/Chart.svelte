<script lang="ts">
    import { scaleLinear } from 'd3-scale';

    export let data = [], width = 500, height = 200, style = "";
	export let xTicks = [0, 1, 2, 3, 4, 5, 6], yTicks = [0, 9, 18];
    export let padding = { top: 20, right: 20, bottom: 20, left: 30 };

    let day = ["월", "화", "수", "목", "금", "토", "일"]

	$: xScale = scaleLinear()
		.domain([minX, maxX])
		.range([padding.left, width - padding.right]);

	$: yScale = scaleLinear()
		.domain([Math.min.apply(null, yTicks), Math.max.apply(null, yTicks)])
		.range([height - padding.bottom, padding.top]);

	$: minX = data[0].x;
	$: maxX = data[data.length - 1].x;
	
	$: path = `M${data.map(p => `${xScale(p.x)},${yScale(p.y)}`).join('L')}`;
	$: area = `${path}L${xScale(maxX)},${yScale(0)}L${xScale(minX)},${yScale(0)}Z`;
</script>

<div class="chart" style={style} bind:clientWidth={width} bind:clientHeight={height}>
	<svg>
		<g class="axis y-axis" transform="translate(0, {padding.top})">
			{#each yTicks as tick}
				<g class="tick tick-{tick}" transform="translate(0, {yScale(tick) - padding.bottom})">
					<text style="font-size: 0.75rem;" x="4" y="4">{tick}h</text>
				</g>
			{/each}
		</g>

		<g class="axis x-axis">
			{#each xTicks as tick}
				<g class="tick tick-{ tick }" transform="translate({xScale(tick)},{height})">
					<line y1="-{height}" y2="-{padding.bottom}" x1="0" x2="0"></line>
					<text style="font-size: 0.875rem;" x="-1" y="2">{day[tick]}</text>
				</g>
			{/each}
		</g>

		<path class="path-area" d={area}></path>
		<path class="path-line" d={path}></path>
	</svg>
</div>


<style>
	.chart{
        max-width: 500px;
        margin: auto;
        background-color: #28222d;
	}

	svg {
		position: relative;
		width: 100%;
		height: 200px;
		overflow: visible;
	}

    text{
        font-family: NotoSansKR;
        font-weight: 300;
        fill: #ffffff;
    }

	.tick {
		font-size: .725em;
		font-weight: 200;
	}

	.tick line {
		stroke: #aaa;
		stroke-dasharray: 2;
	}

	.tick.tick-0 line {
		stroke-dasharray: 0;
	}

	.x-axis .tick text {
		text-anchor: middle;
	}

	.path-line {
		fill: none;
		stroke: #ffffff;
		stroke-linejoin: round;
		stroke-linecap: round;
		stroke-width: 2;
	}

	.path-area {
		fill: rgba(255,255,255,0.2);
	}
</style>