<script>
  import { getContext } from "svelte";
  import { format } from "d3-format";
  import { descriptions, department } from "./data/data";

  console.log(descriptions);

  export let w, h, hovered, formatPercent, formatDollars;

  //   const { width, height } = getContext("LayerCake");
  const offset = 10;
  let tooltipWidth, tooltipHeight;

  $: if (hovered) {
    // console.log(rootHeight);
    // console.log(hovered.data);
    // console.log(formatDollars(hovered.data.value));
  }
</script>

<div
  bind:clientWidth={tooltipWidth}
  bind:clientHeight={tooltipHeight}
  class={`tooltip`}
  class:active={hovered}
  style={hovered &&
    hovered.e &&
    `top: ${
      hovered.e.clientY + tooltipHeight / 2 > h
        ? hovered.e.clientY - tooltipHeight
        : hovered.e.clientY - tooltipHeight / 2
    }px; left: ${
      hovered.e.clientX + tooltipWidth > w
        ? hovered.e.clientX - tooltipWidth < 0
          ? hovered.e.clientX - tooltipWidth / 2
          : hovered.e.clientX - tooltipWidth - offset
        : hovered.e.clientX + offset
    }px;`}
>
  {#if hovered}
    <div class="title">{department[hovered.data.department]}</div>
    <div class="description">
      {descriptions[department[hovered.data.department]]}
    </div>
    <div class="value">
      {formatDollars(hovered.data["2022"])}
      <span
        class={`change ${hovered.data.change > 0 ? "positive" : "negative"}`}
        >{formatPercent(hovered.data.change)}</span
      >
    </div>{/if}
</div>

<style>
  .tooltip {
    border: 1px solid var(--gray-light);
    border-radius: 3px;
    position: absolute;
    visibility: hidden;
    pointer-events: none;
    padding: 0.75rem;
    background-color: rgba(255, 255, 255, 0.9);
    width: 240px;
    z-index: 99;
  }
  .tooltip.active {
    visibility: visible;
  }

  .title {
    font-size: 0.85rem;
    font-weight: 600;
  }

  .description {
    font-size: 0.75rem;
    color: #666;
  }

  .value {
    font-size: 1.35rem;
    font-weight: 600;
  }

  .change.positive {
    color: green;
  }

  .change.negative {
    color: red;
  }
</style>
