<script>
// @ts-nocheck

    import CarDropdown from './CarDropdown.svelte';
    import 'bootstrap/dist/css/bootstrap.min.css';
    const name = "Federico Soldati";
    const university = "KU Leuven";
    const studentNumber = "r0924528";
    import { scaleOrdinal } from 'd3-scale';

    export let data;
    export let selectedCar;
    let selectedCarId;
    $: selectedCarId = selectedCar ? parseInt(selectedCar.slice(4)) : null;

    
    const colorScale = scaleOrdinal()
      .domain([...new Set(data.Points.map(v => v.type))])
      .range(["red","green","black","yellow","blue"]);

    const rescale = function(x, domain_min, domain_max, range_min, range_max) {
    return ((range_max - range_min)*(x-domain_min))/(domain_max-domain_min) + range_min
    }

</script>

<h1>{name} - {university} - {studentNumber}</h1>
<p style="font-size: 24px"><strong> Overview </strong></p>

<div style="display: flex; align-items: center; font-size: 14px;">
  <p style="margin-right: 14px;">Select car to highlight:</p>
  <CarDropdown data={data.GPS} bind:selectedCar/>
</div>

{#if selectedCar != ""}
  <p style="font-size: 14px">
    Go to 
    <a href="/{selectedCarId}">
      <button class="btn btn-primary btn-sm">Detail</button>
    </a>
    for {selectedCar}
  </p>
{/if}

<main>  
  <svg style="position: absolute; left: 50; top: 200;" width="700" height="700" >
    <g transform="translate(10,10)"> 
      {#each data.GPS as gpsData}
        <circle
          cx={rescale(gpsData.long, 24.82587, 24.90836, 0, 600)}
          cy={rescale(gpsData.lat, 36.04802, 36.08961, 600, 0)}
          r=1.5
          fill="black"
          opacity={0.2}
        /> 
      {/each}
  
      {#each data.Points as points}
        <circle
          cx={rescale(points.long, 24.82587, 24.90836, 0, 600)}
          cy={rescale(points.lat, 36.04802, 36.08961, 600, 0)}
          r=6
          fill={colorScale(points["type"])}
          opacity={1}>
            <title>{points.name}</title>
        </circle> 
      {/each}

      {#each data.GPS as gpsData}
        {#if gpsData.car_id == selectedCarId}
          <circle
            cx={rescale(gpsData.long, 24.82587, 24.90836, 0, 600)}
            cy={rescale(gpsData.lat, 36.04802, 36.08961, 600, 0)}
            r=2
            fill="red"
            opacity={1}
          /> 
        {/if}
      {/each}
    </g>
  </svg>
</main>
