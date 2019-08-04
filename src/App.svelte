
<script>
	let privateCar = true;
	
	let taxPressure = 0.5;
	$: taxMult = (1 / taxPressure); 
	
	let monthsPrivate = true;
	let months = 60;
	
	let insurancePrivate = true;
	let insurance = 62;
	$: insuranceTotal = (insurance * months * (insurancePrivate ? taxMult : 1));
	
	let roadTaxPrivate = true;
	let roadTax = 74;
	$: roadTaxTotal = (roadTax * months * (roadTaxPrivate ? taxMult : 1));
	
	let fuelPrivate = true;
	let fuel = 150;
	$: fuelTotal = (fuel * months * (fuelPrivate ? taxMult : 1));
	
	let main_py_private = true;
	let main_py = 1000; // Per year
	$: mainTotal = ((main_py / 12) * months * (main_py_private ? taxMult : 1));
	
	let kmsVerg_py_private = true;
	let kmsVerg_py = 2000;
	$: kmsVergTotal = ((kmsVerg_py / 12) * months * (kmsVerg_py_private ? taxMult : 1));
	
	const bijtellingPrivate = true;
	let bijtelling = 0;
	$: bijtellingTotal = (bijtelling * months * (bijtellingPrivate ? taxMult : 1));
	
	let writeOffPerYearPrivate = true;
	let writeOffPerYear = 2000;
	$: writeOffPerYearTotal = ((writeOffPerYear / 12) * months * (writeOffPerYearPrivate ? taxMult : 1));
	
	$: totalPrivate = insuranceTotal
		+ roadTaxTotal
		+ fuelTotal
		+ mainTotal
		+ bijtellingTotal
		+ writeOffPerYearTotal
		- kmsVergTotal;
</script>

<h1>dga calc</h1>

<style>
	.gritty {
		display: grid;
		grid-template-columns: repeat(auto-fill, minmax(84px, 1fr));
	}

	.label { font-size: 11px; color: #333; text-transform: uppercase; letter-spacing: 0.2px; font-weight: bold; }
	
	.input-text { height: 26px; font-size: 14px; }
	.input-text:hover { box-shadow: 0 0 0 2px grey; }
	.input-text:focus { box-shadow: 0 0 0 2px blue; }
	.t-r { text-align: right; }
	.w-62 { width: 62px; }
	.w-62 { width: 62px; }
</style>


<label>Maanden: {months} ({(months / 12).toFixed(1)} jaar)</label>
<input type="range" min="0" max="60" bind:value="{months}"/>

<div class="gritty">
	<p class="gritty-item">
		<label class="label">
			Bijtelling
		</label>
		<input bind:value="{bijtelling}" type="number" class="input-text w-62 t-r"><br/>
		<strong>{ months * bijtelling }</strong>
	</p>
	<p>
		<label class="label">
			Verzekering
		</label>
		<input bind:value="{insurance}" type="number" class="input-text w-62 t-r">
		<input type="checkbox" bind:checked="{insurancePrivate}">
			<br/>
		<strong>{ months * insurance }</strong>
	</p>
	<p>
		<label class="label">
			Wegenbelasting
		</label>
		<input bind:value="{roadTax}" type="number" class="input-text w-62 t-r">
		<input type="checkbox" bind:checked="{roadTaxPrivate}">
		<br/>
		<span>{ 12 * roadTax} p/j</span>
		<span>{ months * roadTax }</span>
		<strong>{ roadTaxTotal.toFixed(2) }</strong>
	</p>

	<div>
	
		<hr />
		€ {(totalPrivate * -1).toFixed(2)} Benodigd bruto inkomen
	</div>
</div>

	<h5>
		Brandstof
		<input type="checkbox" bind:checked="{fuelPrivate}">
	</h5>
	<input type="number" bind:value="{fuel}"><br/>
	<strong>{ months * fuel }</strong>

	<h5>
		Onderhoud
		<input type="checkbox" bind:checked={main_py_private}/>
	</h5>
	<input type="number" bind:value="{main_py}"><br/>
	<strong>{ months * (main_py / 12) }</strong>

	<h5>
		Kilometervergoeding
		<span style="color: #999">{(kmsVerg_py / 0.19).toFixed(2)} km/j</span>
	</h5>
	<input type="number" bind:value="{kmsVerg_py}"><br/>
	<strong>{ months * (kmsVerg_py / 12) } </strong>

	<h5>
		Afschrijving per jaar
	</h5>
	<input type="number" bind:value="{writeOffPerYear}"><br/>
	<strong>{ months * (writeOffPerYear / 12) }</strong>



