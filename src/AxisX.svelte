<script>
  import { getContext } from "svelte";
  const {
    padding,
    containerWidth,
    containerHeight,
    width,
    height,
    xScale,
    yRange,
  } = getContext("LayerCake");
  //   console.log($width);
  //   console.log($width / 125);
  export let gridlines = true;
  export let tickMarks = false;
  export let formatTick = (d) => d;
  export let baseline = false;
  export let snapTicks = false;
  export let ticks = 5;
  export let xTick = undefined;
  export let yTick = 16;
  export let dxTick = 0;
  export let dyTick = 0;
  export let axisLabel = "";
  $: isBandwidth = typeof $xScale.bandwidth === "function";
  //   $: console.log(ticks);
  //   $: console.log($xScale.ticks());
  $: tickVals = Array.isArray(ticks)
    ? ticks
    : isBandwidth
    ? $xScale.domain()
    : typeof ticks === "function"
    ? ticks($xScale.ticks())
    : $xScale.ticks(ticks);
  function textAnchor(i) {
    if (snapTicks === true) {
      if (i === 0) {
        return "start";
      }
      if (i === tickVals.length - 1) {
        return "end";
      }
    }
    return "middle";
  }
</script>

<text class="axis-label" x={$width / 2} y={$height + $padding.bottom}
  >{axisLabel}</text
>
<g class="axis x-axis" class:snapTicks>
  {#each tickVals as tick, i}
    <g
      class="tick tick-{i}"
      transform="translate({$xScale(tick)},{$yRange[0]})"
    >
      {#if gridlines !== false}
        <line class="gridline" y1={$height * -1} y2="0" x1="0" x2="0" />
      {/if}
      {#if tickMarks === true}
        <line
          class="tick-mark"
          y1={0}
          y2={6}
          x1={xTick || isBandwidth ? $xScale.bandwidth() / 2 : 0}
          x2={xTick || isBandwidth ? $xScale.bandwidth() / 2 : 0}
        />
      {/if}
      <text
        x={xTick || isBandwidth ? $xScale.bandwidth() / 2 : 0}
        y={yTick}
        dx={dxTick}
        dy={dyTick}
        text-anchor={textAnchor(i)}>{formatTick(tick)}</text
      >
    </g>
  {/each}
  {#if baseline === true}
    <line
      class="baseline"
      y1={$height + 0.5}
      y2={$height + 0.5}
      x1="0"
      x2={$width}
    />
  {/if}
</g>

<style>
  .tick {
    font-size: 0.725em;
    font-weight: 200;
  }
  line,
  .tick line {
    stroke: #aaa;
    stroke-dasharray: 2;
  }
  .tick text {
    fill: #666;
  }
  /* .tick.tick-0 line {
      stroke-dasharray: 0;
    } */
  .tick .tick-mark,
  .baseline {
    stroke-dasharray: 0;
  }
  /* This looks slightly better */
  .axis.snapTicks .tick:last-child text {
    transform: translateX(3px);
  }
  .axis.snapTicks .tick.tick-0 text {
    transform: translateX(-3px);
  }
  .axis-label {
    text-anchor: middle;
    text-transform: uppercase;
    letter-spacing: 1px;
    font-weight: 300;
    fill: var(--gray-dark);
  }
</style>
