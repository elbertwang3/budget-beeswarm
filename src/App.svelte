<script>
  import { LayerCake, Svg } from "layercake";
  import { data } from "./data/data";
  import {
    scaleOrdinal,
    scaleSequential,
    scaleSqrt,
    scaleThreshold,
  } from "d3-scale";
  import { schemeRdYlGn } from "d3-scale-chromatic";
  import { extent, max, group } from "d3-array";
  import { format } from "d3-format";
  import AxisX from "./AxisX.svelte";
  import Beeswarm from "./Beeswarm.svelte";
  import Tooltip from "./Tooltip.svelte";
  import Key from "./Key.svelte";

  let w, h, container;
  let hovered, nodes;

  const grouped = group(data, (d) => d.organization_group);
  // $: console.log(hovered);

  let xKey = "change",
    rKey = "2022",
    zKey = "change";

  function formatDollars(d) {
    return format("$0.3s")(d).replace(/G/, "B").toLowerCase();
  }

  const formatPercent = format(".0%");

  const zDomain = [-0.25, -0.1, 0, 0.1, 0.25, 0.5];
  const zRange = schemeRdYlGn[7];

  //   const zDomain = [
  //     "Public Works, Transportation & Commerce",
  //     "Human Welfare & Neighborhood Development",
  //     "Community Health",
  //     "Public Protection",
  //     "General Administration & Finance",
  //     "General City Responsibilities",
  //     "Culture & Recreation",
  //   ];

  //   const zRange = [
  //     "#f15f27",
  //     "#25c2e4",
  //     "#ffca42",
  //     "#ffebe3",
  //     "#d6f8ff",
  //     "#efbbcc",
  //     "#7DDF64",
  //   ];
</script>

<main>
  <!-- <div class="chart-container">
    <LayerCake
      padding={{ top: 15, right: 15, bottom: 50, left: 15 }}
      x={xKey}
      z={zKey}
      r={rKey}
      rScale={scaleSqrt()}
      rRange={[3, 50]}
      zScale={scaleThreshold()}
      zDomain={[-0.25, -0.1, 0, 0.1, 0.25, 0.5]}
      zRange={schemeRdYlGn[7]}
      {data}
      let:width
    >
      <Svg>
        <Beeswarm strokeWidth={1} xStrength={0.95} yStrength={0.075} />
      </Svg>
    </LayerCake>
  </div> -->
  <div class="key">
    <div class="legend-title">Change in budget from previous year</div>
    <Key {zDomain} {zRange} {formatPercent} />
  </div>
  <div
    class="grid"
    bind:clientWidth={w}
    bind:clientHeight={h}
    bind:this={container}
  >
    {#each [...grouped] as [key, value]}
      <div class="chart-container">
        <div class="title">{key}</div>
        <div class="chart">
          <LayerCake
            padding={{ top: 0, right: 0, bottom: 0, left: 0 }}
            x={xKey}
            z={zKey}
            r={rKey}
            rScale={scaleSqrt()}
            rDomain={[0, max(data, (d) => d["2022"])]}
            rRange={[1, 40]}
            zScale={scaleThreshold()}
            {zDomain}
            {zRange}
            data={value}
            let:width
          >
            <Svg>
              <!-- <AxisX formatTick={formatPercent} {xKey} /> -->
              <Beeswarm bind:hovered />
              <!-- <Voronoi bind:hovered {nodes} /> -->
            </Svg>

            <!-- <Html pointerEvents={false}>
  </Html> -->
          </LayerCake>
        </div>
      </div>
    {/each}
    {#if container}
      <Tooltip {w} {h} {container} {hovered} {formatPercent} {formatDollars} />
    {/if}
  </div>
</main>

<style>
  main {
    width: 100%;
    height: 100%;
    font-family: "Amiko", sans-serif;
  }

  .grid {
    position: relative;
    max-width: 800px;
    margin: auto;
    display: grid;
    grid-gap: 1rem;
    grid-template-columns: repeat(auto-fit, minmax(160px, 1fr));
    height: auto;
  }

  .key {
    max-width: 252px;
    margin: 0 auto 1rem auto;
  }

  .legend-title {
    font-size: 0.85rem;
    font-weight: 600;
    text-align: center;
    margin-bottom: 0.25rem;
  }

  .chart-container {
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    /* height: 200px; */
  }
  .title {
    font-size: 0.85rem;
    text-align: center;
    z-index: 2;
  }
  .chart {
    z-index: 1;
    height: 125px;
  }
</style>
