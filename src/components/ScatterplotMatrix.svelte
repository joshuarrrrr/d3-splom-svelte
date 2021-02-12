<script>
  import * as d3 from "d3";

  export let data = [];
  export let columns = [];
  let width = 500;
  let padding = 30;

  $: size = (width - (columns.length + 1) * padding) / columns.length + padding;
  $: x = columns.map(c =>
    d3
      .scaleLinear()
      .domain(d3.extent(data, d => d[c]))
      .rangeRound([padding / 2, size - padding / 2])
  );
  $: y = x.map(x => x.copy().range([size - padding / 2, padding / 2]));
  $: z = d3
    .scaleOrdinal()
    .domain(data.map(d => d.species))
    .range(d3.schemeTableau10);
</script>

<style>
  svg {
    max-width: 100%;
    height: auto;
  }

  rect.cell {
    fill: none;
    stroke: #aaa;
  }

  circle {
    fill-opacity: 0.7;
  }

  line {
    stroke: #ddd;
  }

  .axis {
    font-size: 10px;
    font-family: sans-serif;
  }

  .x-axis {
    text-anchor: middle;
  }

  .y-axis {
    text-anchor: end;
  }
</style>

<div class="scatterplot" bind:clientWidth={width}>
  <svg {width} height={width} viewBox="{-padding} 0 {width} {width}">
    {#each x as scale, i}
      <g class="axis x-axis" transform="translate({i * size} 0)">
        {#each scale.ticks(6) as tick}
          <line x1={scale(tick)} x2={scale(tick)} y2={columns.length * size} />
          <text
            class="tick"
            dy="0.71em"
            x={scale(tick)}
            y={columns.length * size + 3}>
            {scale.tickFormat(6)(tick)}
          </text>
        {/each}
      </g>
    {/each}

    {#each y as scale, i}
      <g class="axis y-axis" transform="translate(0 {i * size})">
        {#each scale.ticks(6) as tick}
          <line x2={columns.length * size} y1={scale(tick)} y2={scale(tick)} />
          <text class="tick" dy=".32em" x="-3" y={scale(tick)}>
            {scale.tickFormat(6)(tick)}
          </text>
        {/each}
      </g>
    {/each}

    {#each d3.cross(d3.range(columns.length), d3.range(columns.length)) as [i, j]}
      <g transform="translate({i * size} {j * size})">
        <rect
          class="cell"
          x={padding / 2 + 0.5}
          y={padding / 2 + 0.5}
          width={size - padding}
          height={size - padding} />
        {#each data.filter(d => !isNaN(d[columns[i]]) && !isNaN(d[columns[j]])) as d}
          <circle
            cx={x[i](d[columns[i]])}
            cy={y[j](d[columns[j]])}
            r="5.5"
            fill={z(d.species)} />
        {/each}
      </g>
    {/each}

    {#each columns as name, i}
      <g transform="translate({i * size} {i * size})">
        <text x={padding} y={padding} dy=".71em">{name}</text>
      </g>
    {/each}
  </svg>
</div>
