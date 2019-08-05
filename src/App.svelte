
<script>
  let carPrivate = false;
  let carValue = 0;
  $: carValueBusiness = carPrivate ? 0 : carValue;
  $: carValuePrivate = carPrivate ? carValue * taxMult : 0;
  let carValueRisidual = 0;
  
  let taxPressure = 0.52;
  // General settings
  $: taxMult = (1 / taxPressure);
  let months = 60;
  let kms_py = 17500; 
  let kms_ratio_private = 0.8;
  let kms_verg_private = 0.19;
  
  // Leasebedrag
  let lease_pm = 0;
  let leasePrivate = false;
  $: lease_py = lease_pm * 12;
  $: leaseTotal = lease_pm * months;
  $: leaseTotalBusiness = leasePrivate ? 0 : leaseTotal;
  $: leaseTotalPrivate = leasePrivate ? (leaseTotal * taxMult) : 0;
  
  // Afschrijving
  let writeOffPrivate = true;
  let writeOff = 2000;
  $: writeOffTotal = (writeOff / 12) * months;
  $: writeOffTotalBusiness = writeOffPrivate ? 0 : writeOffTotal;
  $: writeOffTotalPrivate = writeOffPrivate ? (writeOffTotal * taxMult) : 0;

  // Bijtelling
  let bijtelling = 0;
  $: bijtellingTotal = (bijtelling / 12) * months;
  // Bijtelling is always private
  $: bijtellingTotalPrivate = bijtellingTotal * taxMult;

  // Verzekering
  let insurancePrivate = true;
  let insurance = 62;
  $: insuranceTotal = insurance * months;
  $: insuranceTotalBusiness = insurancePrivate ? 0 : insuranceTotal;
  $: insuranceTotalPrivate = insurancePrivate ? insuranceTotal * taxMult : 0;
  
  // Wegenbelasting
  let roadTaxPrivate = true;
  let roadTax = 67;
  $: roadTaxTotal = roadTax * months;
  $: roadTaxTotalBusiness = roadTaxPrivate ? 0 : roadTaxTotal;
  $: roadTaxTotalPrivate = roadTaxPrivate ? roadTaxTotal * taxMult : 0;
  
  // Maintenance
  let main_py_private = true;
  let main_py = 1000; // Per year
  $: mainTotal = (main_py / 12) * months;
  $: mainTotalBusiness = main_py_private ? 0 : mainTotal;
  $: mainTotalPrivate = main_py_private ? mainTotal * taxMult : 0;
  
  // Fuel
  let unitCost = 1.70;
  let unitKms = 21;
  let fuelPrivate = true;
  $: unitKmCost =  unitCost / unitKms;
  $: fuelTotalPY = (unitKmCost * kms_py);
  $: fuelTotal = (fuelTotalPY / 12) * months;
  $: fuelTotalInput = fuelTotalPY.toFixed(2);
  $: fuelTotalBusiness = fuelPrivate ? 0 : fuelTotal;
  $: fuelTotalPrivate = fuelPrivate ? fuelTotal * taxMult : 0;

  // Kms veroeding
  $: km_verg_py = bijtelling > 0 ? 0 : kms_py * kms_ratio_private * kms_verg_private;
  $: km_verg_total = bijtelling > 0 ? 0 : (km_verg_py / 12) * months;
  
  $: total = 0
    + carValue
    + leaseTotal
    + writeOffTotal
    + insuranceTotal
    + roadTaxTotal
    + mainTotal
    + fuelTotal;

  $: totalBusiness = 0
    + carValueBusiness
    + leaseTotalBusiness
    + writeOffTotalBusiness
    + insuranceTotalBusiness
    + roadTaxTotalBusiness
    + mainTotalBusiness
    + fuelTotalBusiness;

  $: totalBusinessBtw = (totalBusiness - ((totalBusiness / 121) * 100));
  $: totalBusinessWB = (totalBusiness - totalBusinessBtw) * 0.19;
  $: totalBusinessAfterTax = totalBusiness - totalBusinessBtw - totalBusinessWB;

  $: totalPrivate = 0
    + carValuePrivate
    + leaseTotalPrivate
    + writeOffTotalPrivate
    + bijtellingTotalPrivate
    + insuranceTotalPrivate
    + roadTaxTotalPrivate
    + mainTotalPrivate
    + fuelTotalPrivate;
    
</script>

<style>
  .general-settings {
    display: flex;
    background: #eee;
    padding: 14px 20px;
  }

  .general-settings > * { margin: 0; width: 262px; }

  .btn-pres { display: flex; }
  .btn-pre { background: #ddd; color: #666; font-size: 11px; margin-left: 1px; padding: 1px 5px; border-radius: 0; border: 0; transition: 262ms ease-out; }
  .btn-pre:first-child { border-radius: 3px 0 0 3px; margin-left: 0; }
  .btn-pre:last-child { border-radius: 0 3px 3px 0; }
  .btn-pre.isActive { background: blue; color: #fff; transition: none; }

  .calc-header {
    margin: 20px 0 0 78px;
  }

  .gritty {
    display: grid;
    margin-top: 14px;
    grid-column-gap: 16px;
    grid-template-columns: 62px repeat(10, 84px) repeat(1, 1fr);
    /* 
			What happens here:
			We're repeating 5 times a column of 120px and 3 times a column of 1fr.
			this is the same as writing:
			120px 120px 120px 120px 120px 1fr 1fr 1fr. 
			Every one of these can also have any other preferred value.
     */
  }

  .gritty-item {

  }

  .label {
    font-size: 11px;
    color: #333;
    text-transform: uppercase;
    letter-spacing: 0.2px;
    font-weight: bold;
  }

  .label-normal {
	  text-transform: none; letter-spacing: 0;
	}

  .input-text {
    height: 26px;
    font-size: 14px;
  }

  .input-text[type="number"] {
    padding-right: 1px;
  }

  .input-text---tiny {
    height: 20px;
    font-size: 12px;
  }

  .input-text:disabled {
    background: #eee;
    color: #333;
  }

  .input-text:hover {
    box-shadow: 0 0 0 2px grey;
  }
  .input-text:focus {
    box-shadow: 0 0 0 2px blue;
  }
  .t-r {
    text-align: right;
  }

  .t-ellipsis { overflow: hidden; text-overflow: ellipsis; white-space: nowrap; }

  .w-48 {
    width: 48px;
  }
  .w-60 {
    width: 60px;
  }
  .w-62 {
    width: 84px;
  }

  .m-b-xxs { margin-bottom: 2px; }
  .m-b-xs { margin-bottom: 4px; }

  .d-b { display: block; }

  .b-t-sub { border-top: 1px solid #999;}
  .b-t-sub-sub { border-top: 1px solid #ddd;}

  .sum-placeholder {
    padding: 2px 0 2px 0;
    font-size: 13px;
    text-align: right;
  }

  .sum-placeholder.faded {
    color: #999;
  }

  .sum {
    padding: 2px 0 2px 0;
    position: relative;
    font-size: 13px;
    text-align: right;
  }

  .sum:before {
    content: "€";
    position: absolute;
    top: 3px;
    left: 0;
    font-size: 11px;
    color: #999;
  }
</style>

<section class="general-settings">
  <div>
    <label class="label">Looptijd: <span class="label-normal">{months} maanden ({(months / 12).toFixed(1)} jaar)</span></label><br />
    <input type="range" min="0" max="120" bind:value="{months}"/>
    <div class="btn-pres">
      <button on:click={() => { months = 12; }} class="btn-pre" class:isActive={months === 12}>1 jr</button>
      <button on:click={() => { months = 12 * 2; }} class="btn-pre" class:isActive={months === 12 * 2}>2 jr</button>
      <button on:click={() => { months = 12 * 3; }} class="btn-pre" class:isActive={months === 12 * 3}>3 jr</button>
      <button on:click={() => { months = 12 * 5; }} class="btn-pre" class:isActive={months === 12 * 5}>5 jr</button>
      <button on:click={() => { months = 12 * 10; }} class="btn-pre" class:isActive={months === 12 * 10}>10 jr</button>
    </div>
  </div>

  <div>
    <label class="label">Kilometers p/j: <span class="label-normal">{kms_py} km</span></label><br />
    <input type="range" min="0" max="100000" step="500" bind:value="{kms_py}"/>
    <div class="btn-pres">
      <button on:click={() => { kms_py = 10000; }} class="btn-pre" class:isActive={kms_py === 10000}>10</button>
      <button on:click={() => { kms_py = 15000; }} class="btn-pre" class:isActive={kms_py === 15000}>15</button>
      <button on:click={() => { kms_py = 25000; }} class="btn-pre" class:isActive={kms_py === 25000}>25</button>
      <button on:click={() => { kms_py = 35000; }} class="btn-pre" class:isActive={kms_py === 35000}>35</button>
      <button on:click={() => { kms_py = 50000; }} class="btn-pre" class:isActive={kms_py === 50000}>50</button>
      <button on:click={() => { kms_py = 70000; }} class="btn-pre" class:isActive={kms_py === 70000}>70</button>
    </div>
  </div>

  <p>
    <label class="label">Privé kilometers ratio: <span class="label-normal">{(kms_ratio_private * 100).toFixed(0)} %</span></label><br />
    <input type="range" min="0" max="1" step="0.05" bind:value="{kms_ratio_private}"/>
  </p>

  <p>
    <label class="label">Privé kilometers verg: <span class="label-normal">€ {(kms_verg_private).toFixed(2)}</span></label><br />
    <input type="range" min="0" max="1" step="0.01" bind:value="{kms_verg_private}"/>
  </p>
</section>

<div class="calc-header">
  Autokosten. &nbsp; Waarde:
  <input bind:value={carValue} type="number" class="input-text input-text---tiny w-60 t-r" />
  <label class="label"><input type="checkbox" bind:checked={carPrivate}> Privé</label>
  &nbsp; &nbsp; &nbsp;
  <input bind:value={unitKms} type="number" class="input-text input-text---tiny w-48 t-r" />
  kms per €
  <input bind:value={unitCost} type="string" class="input-text input-text---tiny w-48 t-r" />
</div>
 
<div class="gritty">
  <div class="gritty-item">
    <label class="label t-ellipsis d-b m-b-xs">
      &nbsp;
    </label>
    <input style="opacity: 0;" bind:value={lease_pm} type="number" disabled class="input-text w-62 t-r" />
    <div class="sum-placeholder faded" style="">p/j</div>
    <div class="sum-placeholder faded b-t-sub-sub">subtotal</div>

    <label class="label db" style="opacity: 0;"><input type="checkbox" disabled> Privé</label>
    <div class="sum-placeholder b-t-sub">zakelijk</div>
    <div class="sum-placeholder b-t-sub">prive</div>
  </div>

  <div class="gritty-item">
    <label class="label t-ellipsis d-b m-b-xs">
      Leasebedrag
    </label>
    <input bind:value={lease_pm} type="number" class="input-text w-62 t-r" />
    <div class="sum">{lease_py.toFixed(2)}</div>
    <div class="sum b-t-sub-sub">{leaseTotal.toFixed(2)}</div>

    <label class="label db"><input type="checkbox" bind:checked={leasePrivate}> Privé</label>
    <div class="sum b-t-sub">{leaseTotalBusiness.toFixed(2)}</div>
    <div class="sum b-t-sub">{leaseTotalPrivate.toFixed(2)}</div>
  </div>

  <div class="gritty-item">
    <label class="label t-ellipsis d-b m-b-xs">
      Afschrijving per jaar
    </label>
    <input bind:value={writeOff} type="number" class="input-text w-62 t-r" />
    <div class="sum">{writeOff.toFixed(2)}</div>
    <div class="sum b-t-sub-sub">{writeOffTotal.toFixed(2)}</div>

    <label class="label db"><input type="checkbox" bind:checked={writeOffPrivate}> Privé</label>
    <div class="sum b-t-sub">{writeOffTotalBusiness.toFixed(2)}</div>
    <div class="sum b-t-sub">{writeOffTotalPrivate.toFixed(2)}</div>
  </div>

  <div class="gritty-item">
    <label class="label t-ellipsis d-b m-b-xs">
      Bijtelling
    </label>
    <input bind:value={bijtelling} type="number" class="input-text w-62 t-r">
    <div class="sum">{bijtelling.toFixed(2)}</div>
    <div class="sum b-t-sub-sub">{bijtellingTotal.toFixed(2)}</div>
    
    <label class="label db" style="opacity: 0;"><input type="checkbox" checked disabled/> Privé</label>
    <div class="sum b-t-sub">n.v.t.</div>
    <div class="sum b-t-sub">{bijtellingTotalPrivate.toFixed(2)}</div>
  </div>

  <div class="gritty-item">
    <label class="label t-ellipsis d-b m-b-xs">
      Verzekering
    </label>
    <input bind:value={insurance} type="number" class="input-text w-62 t-r">
    <div class="sum">{(12 * insurance).toFixed(2)}</div>
    <div class="sum b-t-sub-sub">{insuranceTotal.toFixed(2)}</div>

    <label class="label db"><input type="checkbox" bind:checked={insurancePrivate} /> Privé</label>
    <div class="sum b-t-sub">{insuranceTotalBusiness.toFixed(2)}</div>
    <div class="sum b-t-sub">{insuranceTotalPrivate.toFixed(2)}</div>
  </div>

  <div class="gritty-item">
    <label class="label t-ellipsis d-b m-b-xs">
      Wegenb.
    </label>
    <input bind:value={roadTax} type="number" class="input-text w-62 t-r" />
    <div class="sum">{12 * roadTax}</div>
    <div class="sum b-t-sub-sub">{roadTaxTotal.toFixed(2)}</div>

    <label class="label db"><input type="checkbox" bind:checked={roadTaxPrivate} /> Privé</label>
    <div class="sum b-t-sub">{roadTaxTotalBusiness.toFixed(2)}</div>
    <div class="sum b-t-sub">{roadTaxTotalPrivate.toFixed(2)}</div>
  </div>

  <div class="gritty-item">
    <label class="label t-ellipsis d-b m-b-xs">
      Onderhoud
    </label>
    <input bind:value={main_py} type="number" class="input-text w-62 t-r">
    <div class="sum">{main_py.toFixed(2)}</div>
    <div class="sum b-t-sub-sub">{mainTotal.toFixed(2)}</div>

    <label class="label db"><input type="checkbox" bind:checked={main_py_private}/> Privé</label>
    <div class="sum b-t-sub">{mainTotalBusiness.toFixed(2)}</div>
    <div class="sum b-t-sub">{mainTotalPrivate.toFixed(2)}</div>
  </div>

  <div class="gritty-item">
    <label class="label t-ellipsis d-b m-b-xs">
      (Brand)stof
    </label>
    <input bind:value={fuelTotalInput} disabled="true" type="number" class="input-text w-62 t-r" />
    <div class="sum">{(fuelTotalPY).toFixed(2)}</div> 
    <div class="sum b-t-sub-sub">{fuelTotal.toFixed(2)}</div>

    <label class="label db"><input type="checkbox" bind:checked={fuelPrivate}/> Privé</label>
    <div class="sum b-t-sub">{fuelTotalBusiness.toFixed(2)}</div>
    <div class="sum b-t-sub">{fuelTotalPrivate.toFixed(2)}</div>
  </div>

  <div class="gritty-item">
    <label class="label t-ellipsis d-b m-b-xs">
      Km-vergoed.
    </label>
    <input bind:value={km_verg_py} disabled="true" type="number" class="input-text w-62 t-r" />
    <div class="sum">{km_verg_py.toFixed(2)}</div>
    <div class="sum b-t-sub-sub">{(months * (km_verg_py / 12)).toFixed(2)}</div>

    <label class="label db"><input type="checkbox" checked disabled/> Privé</label>
    <div class="sum b-t-sub">- {km_verg_total.toFixed(2)}</div>
    <div class="sum b-t-sub">+ {km_verg_total.toFixed(2)}</div>
  </div>
</div>
<br/>
<br/>
<div>
  <div>Zakelijke kosten: € {totalBusiness.toFixed(2)}</div>
  <div>
    <small>
      Minder omzetbelasting: {totalBusinessBtw.toFixed(2)}<br/>
      Minder winstbelasting: {totalBusinessWB.toFixed(2)}<br/>
      Uiteindelijke kosten: <strong>{totalBusinessAfterTax.toFixed(2)}</strong><br/>
    </small>
  </div>
  <hr/>
  <div>
    Privé kosten: € {#if (bijtellingTotalPrivate > 0)}
      {bijtellingTotal.toFixed(2)}
    {:else}
      {total.toFixed(2)}
    {/if}<br/>
    <small>
      Privé kosten (bruto): € {totalPrivate.toFixed(2)}
    </small>
  </div>
  <div>Km-vergoeding: € {bijtelling === 0 ? km_verg_total.toFixed(2) : 0}</div>
</div>
