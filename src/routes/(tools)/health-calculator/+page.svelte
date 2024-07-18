<script>
	let measurements = [
	{
			id: 16,
			name: "Water Intake",
			description:
				"Estimates the daily water intake based on weight.",
			tooltip:
				"Estimates the daily water intake based on weight.",
			icon_class: "fas fa-tint",
			additional_details:
				"It is recommended to drink 35 ml of water per kg of body weight daily.",
			inputs: [{ name: "weight", label: "Weight (kg)", type: "number" }],
			calculate: function (inputs) {
				const weight = inputs.weight;
				return (weight * 35).toFixed(2);
			},
			result: function (value) {
				return `Your recommended daily water intake is ${value} ml`;
			},
		},
		{
			id: 17,
			name: "Protein Intake",
			description:
				"Estimates the daily protein intake based on weight and activity level.",
			tooltip:
				"Estimates the daily protein intake based on weight and activity level.",
			icon_class: "fas fa-dumbbell",
			additional_details:
				"Protein intake recommendations:\n\n- Sedentary: 0.8 g per kg of body weight\n- Light Activity: 1.0 g per kg of body weight\n- Moderate Activity: 1.2 g per kg of body weight\n- High Activity: 1.5 g per kg of body weight",
			inputs: [
				{ name: "weight", label: "Weight (kg)", type: "number" },
				{
					name: "activity_level",
					label: "Activity Level",
					type: "select",
					options: ["Sedentary", "Light Activity", "Moderate Activity", "High Activity"],
				},
			],
			calculate: function (inputs) {
				const weight = inputs.weight;
				let multiplier;
				switch (inputs.activity_level) {
					case "Sedentary":
						multiplier = 0.8;
						break;
					case "Light Activity":
						multiplier = 1.0;
						break;
					case "Moderate Activity":
						multiplier = 1.2;
						break;
					case "High Activity":
						multiplier = 1.5;
						break;
					default:
						multiplier = 1.0;
				}
				return (weight * multiplier).toFixed(2);
			},
			result: function (value) {
				return `Your recommended daily protein intake is ${value} g`;
			},
		},

	];
  
	let selectedMeasurement = null;
	let result = "";
  
	function handleSelect(id) {
	  selectedMeasurement = measurements.find((m) => m.id === id);
	}
  
	function goBack() {
	  selectedMeasurement = null;
	  result = "";
	}
  
	function handleSubmit(event) {
	  event.preventDefault();
	  const inputs = {};
	  selectedMeasurement.inputs.forEach((input) => {
		inputs[input.name] =
		  input.type === "number"
			? parseFloat(event.target[input.name].value)
			: event.target[input.name].value;
	  });
	  result = selectedMeasurement.calculate(inputs);
	}
  </script>
  
  <link
	rel="stylesheet"
	href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.4/css/all.min.css"
  />
  
  {#if selectedMeasurement}
  <button
  on:click={goBack}
  class="flex items-center gap-2 border-0 px-4 py-2 bg-green-500 text-white rounded-lg hover:bg-green-600"
  >
  <i class="fas fa-arrow-left"></i> <!-- Font Awesome icon for back arrow -->
  <span>Go back</span>
  </button>
  
	<div class="calculation bg-gray-800 text-white mt-4 p-4 border-0 rounded-lg">
	  <h2 class="font-bold text-xl">{selectedMeasurement.name}</h2>
	  <hr />
	  <p class="mt-2 text-lg">{selectedMeasurement.description}</p>
	  <form on:submit={handleSubmit} class="flex flex-col gap-4 mt-4">
		{#each selectedMeasurement.inputs as input}
		  <div class="ml-4">
			<label for={input.name}>{input.label} : </label>
			<input
			  class="mt-2 border-0 rounded-lg text-black p-1 font-bold"
			  type={input.type}
			  id={input.name}
			  name={input.name}
			  required
			/>
		  </div>
		{/each}
		<button
		  type="submit"
		  class="border-0 px-4 py-2 bg-blue-500 text-white rounded-lg hover:bg-blue-600 w-min"
		  >Calculate</button
		>
	  </form>
	  {#if result}
		<div
		  id="result"
		  class="mt-4 p-4 bg-green-500 text-white font-bold rounded-lg shadow-lg"
		>
		  {selectedMeasurement.result(result)}
		</div>
		{#if selectedMeasurement.additional_details}
		  <div
			class="mt-4 p-4 bg-gray-700 text-white font-bold rounded-lg shadow-lg"
		  >
			<pre
			  class="whitespace-pre-line">{selectedMeasurement.additional_details}</pre>
		  </div>
		{/if}
	  {/if}
	</div>
  {:else}
	<div class="card-container flex flex-wrap gap-4">
	  {#each measurements as measurement}
		<!-- svelte-ignore a11y-click-events-have-key-events -->
		<div
		  class="card bg-gray-800 text-white hover:bg-gray-900 border-blue-500 h-40 w-60 flex flex-col items-center gap-1 p-4 rounded-lg cursor-pointer transition-colors duration-300 relative"
		  on:click={() => handleSelect(measurement.id)}
		  data-id={measurement.id}
		>
		  <i class="{measurement.icon_class} m-0 p-0 text-5xl"></i><br />
		  <span class="font-bold">{measurement.name}</span>
		  <span
			class="tooltip bg-white text-black text-sm rounded px-2 py-1 bottom bottom-full mb-2 hidden group-hover:block"
			>{measurement.tooltip}</span
		  >
		</div>
  
		<style>
		  .card:hover .tooltip {
			display: block;
		  }
		</style>
	  {/each}
	</div>
  {/if}
  
  <style>
  </style>