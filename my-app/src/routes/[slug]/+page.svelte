<script>    
// @ts-nocheck
        
      const name = "Federico Soldati";
      const university = "KU Leuven";
      const studentNumber = "r0924528";
      import { scaleLinear } from 'd3-scale';
      import { scaleOrdinal } from "d3-scale";
      export let data;
      $: selectedCarId = data.GPS[0].car_id;
      let sliderValue = 0;
      const days = [6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19];

            
        const rescale = function(x, domain_min, domain_max, range_min, range_max) {
        return ((range_max - range_min)*(x-domain_min))/(domain_max-domain_min) + range_min
      }

    $: startTime = sliderValue - 15; 
    $: endTime = sliderValue + 15;

    const yScale = scaleLinear()
      .domain([6, 19]) // set the domain to the range of possible values
      .range([0, 300]); // set the range of the sca
    
    const xScale = scaleLinear()
      .domain([0, 86400])
      .range([0, 300]);

    const colorScale = scaleOrdinal()
      .domain(["catering", "domestic", "housing", "other", "professional"])
      .range(["green", "yellow", "blue", "purple", "red"]);

  $: sliderDay = Math.floor(sliderValue/1440 +6);
  $: sliderSeconds = (sliderValue - 1440*(sliderDay - 6))*60

</script>

<style>
  .navbar {
    display: flex;
    justify-content: flex-start; /* Change justify-content to flex-start */
    background-color: #f8f9fa;
    padding: 10px;
    margin-bottom: 20px;
  }

  .navbar a {
    text-decoration: none;
    color: #333;
    padding: 5px 10px;
    margin-right: 10px;
    border: 1px solid #ccc;
    border-radius: 4px;
  }

  .navbar a:hover {
    background-color: #e9ecef;
  }
</style>

<div class="navbar">
  <a href="/" class="nav-link">Car Overview</a>
  {#if selectedCarId == 1}
    <a href="/{selectedCarId + 1}" class="nav-link">Next Car</a>
  {:else if selectedCarId == 107}
    <a href="/{selectedCarId - 1}" class="nav-link">Previous Car</a>
  {:else if selectedCarId == 35}
    <a href="/{selectedCarId - 1}" class="nav-link">Previous Car</a>
    <a href="/{selectedCarId + 66}" class="nav-link">Next Car</a>
  {:else if selectedCarId == 101}
    <a href="/{selectedCarId - 66}" class="nav-link">Previous Car</a>
    <a href="/{selectedCarId + 3}" class="nav-link">Next Car</a>
  {:else if selectedCarId == 104}
    <a href="/{selectedCarId - 3}" class="nav-link">Previous Car</a>
    <a href="/{selectedCarId + 1}" class="nav-link">Next Car</a>
  {:else}
    <a href="/{selectedCarId - 1}" class="nav-link">Previous Car</a>
    <a href="/{selectedCarId + 1}" class="nav-link">Next Car</a>
  {/if}
</div>


<h1>{name} - {university} - {studentNumber}</h1>
<p style="font-size: 24px"><strong> Details for car {selectedCarId} </strong></p>

<input type="range" min="0" max="20159" bind:value="{sliderValue}" style="width: 400px"/>


<main style="display: flex">  
  <svg style="position: absolute; left: 25; top: 250;" width="300" height="300" >
    {#each data.GPS as gpsData}
      <circle
        cx={rescale(gpsData.long, 24.82587, 24.90836, 0, 300)}
        cy={rescale(gpsData.lat, 36.04802, 36.08961, 300, 0)}
        r=1.5
        fill="blue"
        opacity={0.2}
      /> 
    {/each}

    {#each data.GPS as gpsData}
      {#if startTime < gpsData.cumulative_minute_total}
        {#if endTime > gpsData.cumulative_minute_total}
          <circle
            cx={rescale(gpsData.long, 24.82587, 24.90836, 0, 300)}
            cy={rescale(gpsData.lat, 36.04802, 36.08961, 300, 0)}
            r=3
            fill="red"
            opacity={1}
          /> 
        {/if}
      {/if}
    {/each}
  </svg>

  <svg style="position: absolute; left: 500; top: 250;" width="400" height="400" >
    <g transform="translate(20,0)"> 
      {#each data.CarStops as Stops}
        {#each days as day}
          {#if Stops.start.day == day}
            <rect
              x={xScale(Stops.start.time)}
              y = {yScale(Stops.start.day)}
              width={xScale(Stops.end.time - Stops.start.time)}
              height="20"
              fill={colorScale(Stops.location.location_type)}>
                <title> {Stops.start.time} - {Stops.end.time}: {Stops.location.name} ({Stops.location.location_type}) </title>
            </rect>
          {/if}
        {/each}
      {/each}
      <line x1="0" y1="0" x2="0" y2="330" stroke="grey" />
      <line x1="75" y1="0" x2="75" y2="330" stroke="grey" />
      <line x1="150" y1="0" x2="150" y2="330" stroke="grey" />
      <line x1="225" y1="0" x2="225" y2="330" stroke="grey" />
      <line x1="300" y1="0" x2="300" y2="330" stroke="grey" />

      <rect
        x={xScale(sliderSeconds)}
        y = {yScale(sliderDay)}
        width="5"
        height="20"
        fill={"black"}
      />
    </g>
    
    {#each days as day}
      {#if day < 10}
        <text x=8 y={yScale(day)+15}> {day}</text>
      {:else}
        <text x=0 y={yScale(day)+15}> {day}</text>
      {/if}
    {/each}

    <text x="15" y="345">0</text>
    <text x="90" y="345">6</text>
    <text x="165" y="345">12</text>
    <text x="240" y="345">18</text>
    <text x="315" y="345">24</text>
  </svg>
</main>