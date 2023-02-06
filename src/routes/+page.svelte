<svelte:head>

</svelte:head>

<script>
  //d3 map essentials
  import * as d3 from 'd3';
  import { onMount } from 'svelte';
  import * as topojson from "topojson-client";

  //import components
  import Tooltip from "$lib/components/tooltip.svelte";

  //colour scale
  import { max } from "d3-array";
  import { scaleBand, scaleLinear } from "d3-scale";

  //data imports
  import world from "$lib/data/country_data.json";
  import world2 from "$lib/data/countries-10m.json";
  import data from "$lib/data/Migration_data.json";

  //geopaths
  import { geoOrthographic, geoPath } from "d3-geo";
  import * as d3_composite from "d3-composite-projections";

  //defining the size of the map
  let width = 960;
  let height = 600;

  const colorScalein = scaleLinear()
    .domain([0,max(data, (d) => d.Inbound)])
    .range(["#26362e", "#00ABF0"]);

  const colorScaleout = scaleLinear()
    .domain([0,max(data, (d) => d.Outbound)])
    .range(["#26362e", "#D21404"]);

  $: path = geoPath().projection(null); // This is new!
  $: year = "2022"

  let states = [];
	let counties = []
	let mesh;
	let selected;

  let dates = [
    "2022","2021","2020","2019", "2018", "2017", "2016", "2015", "2014", "2013"
  ];

  let hover;


  onMount(async () => {
  	const us = await fetch('https://cdn.jsdelivr.net/npm/us-atlas@3/counties-albers-10m.json')
  		.then(d => d.json())

  	states = topojson.feature(us, us.objects.states).features;
  	// console.log({ features })

  	counties = topojson.feature(us, us.objects.counties).features;

  	mesh = topojson.mesh(us, us.objects.states, (a, b) => a !== b);
  })

</script>

<style>
  :global(body) {
    background: #FAF9F6;
    font-family: Helvetica, sans-serif;
  }
  .chart-container {
    margin: 0 auto;
  }

  button {
    background-color: #FAF9F6;
    color: black;
    border: 2px solid #e7e7e7;
    padding: 16px 32px;
    text-align: center;
    text-decoration: none;
    display: inline-block;
    font-size: 16px;
    font-weight: 500;
    font-family: Avenir,system-ui,-apple-system,Helvetica,Arial,sans-serif;
    margin: 4px 10px;
    transition-duration: 0.4s;
    cursor: pointer;

  }

  button:hover {
    background-color: #e7e7e7;
  }

</style>

<div>

<h1>US Interstate and Cross-Border {year} Migration Patterns</h1>

<p>Hey Everyone!</p>

<p>This week's challenge was to vizualize <a href="https://data.world/makeovermonday/2023w3">inbound and outbound migration between US states</a></p>

<p>This time we are trying out d3 map chart type, with some interactivity, using buttons to switch the years of migration shown. </p>

<p>The biggest challenge on this one was really learning how to manage geo projections to see the map in different perscepctives using json data.</p>

<p>If I were to do this again, I'd work on the animation between years, so they look more appealing, but for a data Monday not bad :D</p>

</div>

<div>
{#each dates as date}
  <button class="button"on:click={() => {year = date}}>{date}</button>
{/each}
</div>

<div class="chart-container">

{#if hover}
    <Tooltip data={hover} />
{/if}

  <svg width="{width}" height="{height}">
    <!-- Countries -->
    {#each states as feature,i}
      {#each data as mig_state}
        {#if mig_state.Year == year && mig_state.Inbound > "0.55" && feature.properties.name.toUpperCase() == mig_state.State}
          <path d={path(feature)}
          fill="{colorScalein(mig_state.Inbound)}"
          stroke={hover
                ? hover === mig_state
                    ? "black"
                    : "transparent"
                : "#00000090"}}
          on:mouseover={() => hover=mig_state}
          on:focus={() => hover=mig_state}/>
        {:else if mig_state.Year == year && mig_state.Outbound > "0.55" && feature.properties.name.toUpperCase() == mig_state.State}
          <path d={path(feature)}
          fill="{colorScaleout(mig_state.Outbound)}"
          stroke={hover
                ? hover === mig_state
                    ? "black"
                    : "transparent"
                : "#00000090"}}
          on:mouseover={() => hover=mig_state}
          on:focus={() => hover=mig_state}/>
        {:else if mig_state.Year == year && feature.properties.name.toUpperCase() == mig_state.State}
          <path d={path(feature)}
          fill="#808080"
          stroke={hover
                ? hover === mig_state
                    ? "white"
                    : "black"
                : "#00000090"}
          on:mouseover={() => hover=mig_state}
          on:focus={() => hover=mig_state}/>
          {/if}
      {/each}
    {/each}

  </svg>
</div>
