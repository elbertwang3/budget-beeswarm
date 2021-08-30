<script>
  import { Delaunay } from "d3-delaunay";
  import { getContext } from "svelte";
  import { uniques } from "layercake";
  const { data, xGet, rGet, width, height } = getContext("LayerCake");
  export let nodes = [],
    hovered;
  function log(point) {
    console.log(point, point.data);
  }
  $: points = nodes.map((d) => {
    const point = [d.x, d.y];
    point.data = d;
    return point;
  });
  $: uniquePoints = uniques(points, (d) => d.join(), false);
    $: console.log(points);
    $: console.log(uniquePoints);
  $: voronoi = Delaunay.from(points).voronoi([0, 0, $width, $height]);
</script>

<g class="voronoi-group">
  {#each uniquePoints as point, i}
    <path
      class="voronoi-cell"
      d={voronoi.renderCell(i)}
      on:mouseover={(e) => {
        hovered = { e: e, data: point };
      }}
      on:mouseout={() => (hovered = null)}
      on:blur={() => (hovered = null)}
    />
  {/each}
</g>

<style>
  .voronoi-cell {
    fill: none;
stroke: #333;
    pointer-events: all;
  }
  .voronoi-cell:hover {
    /* stroke: #000; */
    cursor: pointer;
  }
</style>
