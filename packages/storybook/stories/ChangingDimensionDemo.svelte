<script lang="ts">
  import { onDestroy, onMount } from "svelte";
  import type { EmbedOptions } from "vega-embed";
  import { Vega } from "svelte-vega";
  import data1 from "./data1.json";
  import spec1 from "./spec1";
  import spec2 from "./spec2";
  import githubImage from "./GitHub.png";

  let options: EmbedOptions = {
    padding: 20,
  };
  let grow = true;
  let interval: ReturnType<typeof setTimeout>;
  let info = "";
  let data = data1;
  let width = 100;
  let height = 100;
  let selectedSpec = spec1;
  $: spec = {
    ...selectedSpec,
    width,
    height,
  };
  const handlers: {
    tooltip: (...args: unknown[]) => void;
  } = { tooltip: (...args) => (info = JSON.stringify(args)) };

  function handleToggleSpec() {
    if (selectedSpec === spec1) {
      selectedSpec = spec2;
    } else {
      selectedSpec = spec1;
    }
  }

  function handleUpdateData() {
    const table = [];
    for (let i = 1; i <= 20; i++) {
      table.push({
        amount: Math.round(Math.random() * 100),
        category: String.fromCharCode(65 + i),
      });
    }
    data = { table };
  }

  onMount(() => {
    interval = setInterval(() => {
      width = (spec.width ? spec.width : 0) + (grow ? 1 : -1);
      height = (spec.height ? spec.height : 0) + (grow ? 1 : -1);
      grow =
        (grow && (spec.width ? spec.width : 0) < 400) ||
        (!grow && (spec.width ? spec.width : 0) === 100);
    }, 10);
  });

  onDestroy(() => {
    clearInterval(interval);
  });
</script>

<main class="content">
  <div class="head">
    <div class="row-content">
      <h3>
        <code>&lt;Vega&gt;</code> Svelte Component
      </h3>
      <button on:click={handleToggleSpec} class="common-button"
        >Toggle Spec</button
      >
      <button on:click={handleUpdateData} class="common-button"
        >Update Data</button
      >
    </div>
    <button
      class="github-button"
      on:click={() => window.open("https://github.com/vega/svelte-vega")}
    >
      <img src={githubImage} alt="github mark" />
      <div>View on GitHub</div>
    </button>
  </div>
  Will recompile when spec changes and update when data changes.
  <Vega {data} {spec} {options} signalListeners={handlers} />
  <div>
    Hover info: <code>{info}</code>
  </div>
</main>

<style>
  .content {
    display: flex;
    flex-direction: column;
    font-family: "SF Pro Text", "Myriad Set Pro", "SF Pro Icons",
      "Helvetica Neue", Helvetica, Arial, sans-serif;
  }

  .row-content {
    display: flex;
    align-items: center;
  }

  .head {
    display: flex;
    align-items: center;
    justify-content: space-between;
  }

  .github-button {
    display: flex;
    align-items: center;
    height: 2em;
    margin: 1em;
    padding: 0.5em;
    border: 1px solid black;
    border-radius: 5px;
  }

  .github-button:active {
    background-color: #eee;
  }

  .common-button {
    display: flex;
    align-items: center;
    height: 2em;
    margin-left: 0.5em;
    padding: 0.5em;
    border: 1px solid #5382b0;
    border-radius: 5px;
  }

  .common-button:active {
    background-color: #cce6ff;
  }

  img {
    height: 100%;
    padding-right: 0.5em;
  }

  button {
    font-family: inherit; /* 1 */
    font-size: 100%; /* 1 */
    line-height: 1.15; /* 1 */
    margin: 0; /* 2 */
    background-color: transparent;
    text-transform: none;
    border: none;
    overflow: visible;
    -webkit-appearance: button;
  }

  button:active {
    color: inherit;
  }
</style>
