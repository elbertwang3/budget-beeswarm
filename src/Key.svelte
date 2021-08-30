<script>
  export let zDomain, zRange, formatPercent;

  const rectWidth = 40;
  const rectHeight = 8;
</script>

<svg width={rectWidth * (zDomain.length + 1)} height={30}>
  <g class="color-legend" transform={`translate(0,0)`}>
    {#each zRange as d, i}
      <rect
        class="legend-rect"
        key={d}
        fill={d}
        width={rectWidth}
        height={rectHeight}
        x={i * rectWidth}
        y={0}
      />
    {/each}
    {#each zDomain as d, i}
      <g
        class="legend-tick"
        key={d}
        transform={`translate(${(i + 1) * rectWidth}, ${0})`}
      >
        <line class="tick-line" x1={0} x2={0} y1={0} y2={rectHeight * 1.5} />
        <text class="tick-text" y={rectHeight * 3}>{formatPercent(d)}</text>
      </g>
    {/each}
  </g>
</svg>

<style>

  .tick-text {
    font-size: 0.75rem;
    text-anchor: middle;
    font-weight: 300;
    fill: #666;
  }

  .tick-line {
    stroke: #666;
  }
</style>
