<script>
  import { getContext } from "svelte";
  import {
    forceSimulation,
    forceX,
    forceY,
    forceCenter,
    forceManyBody,
    forceCollide,
  } from "d3-force";
  import { Delaunay } from "d3-delaunay";
  const { data, width, height, rGet, zGet } =
    getContext("LayerCake");


  export let hovered;


//   export let xStrength = 0.01;
//   export let yStrength = 0.075;
  export let manyBodyStrength = 100;

  // export let strokeWidth = 1;
  // export let strokeColor = "#fff";
  $: simulation = forceSimulation($data)
    // .force(
    //   "x",
    //   forceX()
    //     .x((d) => $xGet(d))
    //     .strength(xStrength)
    // )
    // .force(
    //   "y",
    //   forceY()
    //     .y($height / 2)
    //     .strength(yStrength)
    // )
    .force(
      "collide",
      forceCollide((d) => $rGet(d) + 1)
    )
    .force("center", forceCenter($width / 2, $height / 2))
    .force("charge", forceManyBody().strength(manyBodyStrength))
    .stop();
  $: {
    for (
      let i = 0,
        n = Math.ceil(
          Math.log(simulation.alphaMin()) /
            Math.log(1 - simulation.alphaDecay())
        );
      i < n;
      ++i
    ) {
      simulation.tick();
    }
  }
  $: nodes = simulation.nodes();
  // $: console.log(nodes);

  $: points = nodes.map((d) => {
    const point = [d.x, d.y];
    point.data = d;
    return point;
  });
  $: voronoi = Delaunay.from(points).voronoi([0, 0, $width, $height]);
</script>

<g class="bee-group">
  {#each nodes as node}
    <circle
      class="node"
      class:hovered={hovered && hovered.data.department == node.department}
      cx={node.x}
      cy={node.y}
      r={$rGet(node)}
      fill={$zGet(node)}
    >
      <!-- <title>{$custom.getTitle(node)}</title> -->
    </circle>
  {/each}
</g>
<g class="voronoi-group">
  {#each points as point, i}
    <path
      class="voronoi-cell"
      d={voronoi.renderCell(i)}
      on:mousemove={(e) => {
          console.log(e)
        hovered = { e: e, data: point.data };
      }}
      on:mouseout={() => (hovered = null)}
      on:blur={() => (hovered = null)}
    />
  {/each}
</g>

<style>
  .node {
    stroke: none;
    pointer-events: none;
  }
  .node.hovered {
    stroke: #333;
    stroke-width: 2;
  }

  .voronoi-cell {
    fill: none;
    stroke: none;
    pointer-events: all;
  }
  .voronoi-cell:hover {
    /* stroke: #000; */
    cursor: pointer;
  }
</style>
