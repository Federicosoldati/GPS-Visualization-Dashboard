<script>
// @ts-nocheck


    export let data = []; 
    export let selectedCar = "";
    
    let Desired_output = (data) => {
        let unique_values = data
            .map((item) => item.car_id)
            .filter(
                (value, index, current_value) => current_value.indexOf(value) === index
            );
        return unique_values;
    };

</script>

<div> 
    <select class="btn btn-primary dropdown-toggle" type="button" aria-haspopup="true" aria-expanded="false" data-toggle="dropdown" bind:value={selectedCar} top = "5px">
        {#each Desired_output(data) as d}
            <option> Car {d} </option>  
        {/each} 
    </select>
</div>
