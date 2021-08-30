<script>
  import { getContext } from "svelte";
  import { format } from "d3-format";
  import { descriptions, department } from "./data/data";

  export let w, h, container, hovered, formatPercent, formatDollars;

  let left = container.getBoundingClientRect().left;
  let top = container.getBoundingClientRect().top;

  //   const { width, height } = getContext("LayerCake");
  const offset = 10;
  let tooltipWidth, tooltipHeight;

  $: if (hovered) {
    // console.log(hovered.e.clientX + tooltipWidth - left, w);
    // console.log(hovered.data);
    // console.log(formatDollars(hovered.data.value));
  }

  function calcTop(y, top) {
    // console.log(y, tooltipHeight, top);
    if (y + tooltipHeight - top > h) {
      return y - tooltipHeight - top - offset;
    } else {
      return y - top + offset;
    }
  }

  function calcLeft(x, left) {
    // console.log(x, tooltipWidth, left);
    if (x + tooltipWidth - left > w) {
      if (x - tooltipWidth - left < 0) {
        return x - left - tooltipWidth / 2;
      } else {
        return x - left - tooltipWidth - offset;
      }
    } else {
      return x - left + offset;
    }
  }
</script>

<div
  bind:clientWidth={tooltipWidth}
  bind:clientHeight={tooltipHeight}
  class={`tooltip`}
  class:active={hovered}
  style={hovered &&
    hovered.e &&
    `top: ${calcTop(
      hovered.e.clientY,
      container.getBoundingClientRect().top
    )}px; left: ${calcLeft(
      hovered.e.clientX,
      container.getBoundingClientRect().left
    )}px;`}
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
