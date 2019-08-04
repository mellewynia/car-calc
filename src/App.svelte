
<script>
  let privateCar = true;
  let carCost = 0;
  
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
  $: writeOffTotalBusiness = writeOffPrivate ? 0 : writeOffTotalBusiness;
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
  $: unitKmCost = unitCost / unitKms;
  $: fuelTotal = (unitKmCost * kms_py);
  $: fuelTotalBusiness = fuelPrivate ? 0 : fuelTotal;
  $: fuelTotalPrivate = fuelPrivate ? fuelTotal * taxMult : 0;

  // Kms veroeding
  $: km_verg_py = bijtelling > 0 ? 0 : kms_py * kms_ratio_private * kms_verg_private;
  $: km_verg_total = bijtelling > 0 ? 0 : (km_verg_py / 12) * months;
  
  $: totalBusiness = 0
    + (leasePrivate       ? 0 : lease_pm * months)
    + (insurancePrivate   ? 0 : insurance * months)
    + (roadTaxPrivate     ? 0 : roadTax * months)
    + (main_py_private    ? 0 : (main_py / 12) * months)
    + (writeOffPrivate ? 0 : (writeOff / 12) * months)
    + (fuelPrivate        ? 0 : (fuelTotal / 12) * months)
    // Only km if ! leasePrivate
    + (! leasePrivate     ? (km_verg_py / 12) * months : 0)
  ;

  $: totalPrivate = 0
    + (leasePrivate       ? lease_pm * months : 0)
    + (insurancePrivate   ? insurance * months : 0)
    + (roadTaxPrivate     ? roadTax * months : 0)
    + (main_py_private    ? ((main_py / 12) * months) : 0)
    + (writeOffPrivate ? (writeOff / 12) * months : 0)
    + ((bijtelling / 12) * months)
    + (fuelPrivate        ? (fuelTotal / 12) * months : 0)
  ;

  $: totalPrivateBeforeTaxes = totalPrivate * taxMult;
</script>

<style>
  .general-settings {
    display: flex;
    background: #eee;
    padding: 14px 20px;
  }

  .general-settings > * { margin: 0; width: 262px; }

  .calc-header {
    margin: 20px 0 0 78px;
  }

  .gritty {
    display: grid;
    margin-top: 14px;
    grid-column-gap: 16px;
    grid-template-columns: 62px repeat(10, 94px) repeat(1, 1fr);
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
    width: 94px;
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
  .sum {
    padding: 2px 18px 2px 0;
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

  .sum__right {
    position: absolute;
    top: 3px;
    right: 0;
    font-size: 11px;
    color: #999;
  }
</style>

<section class="general-settings">
  <p>
    <label class="label">Looptijd: <span class="label-normal">{months} maanden ({(months / 12).toFixed(1)} jaar)</span></label><br />
    <input type="range" min="0" max="120" bind:value="{months}"/><br/>
    <button on:click={() => { months = 12; }}>1 jr</button>
    <button on:click={() => { months = 12 * 2; }}>2 jr</button>
    <button on:click={() => { months = 12 * 3; }}>3 jr</button>
    <button on:click={() => { months = 12 * 5; }}>5 jr</button>
    <button on:click={() => { months = 12 * 10; }}>10 jr</button>
  </p>

  <p>
    <label class="label">Kilometers p/j: <span class="label-normal">{kms_py} km</span></label><br />
    <input type="range" min="0" max="100000" step="500" bind:value="{kms_py}"/>
  </p>

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
  Toyota Auris
  <input bind:value={carCost} type="number" class="input-text input-text---tiny w-60 t-r" />

  <input bind:value={unitKms} type="number" class="input-text input-text---tiny w-48 t-r" />
  kms per
  <input bind:value={unitCost} type="string" class="input-text input-text---tiny w-48 t-r" />
</div>
 
<div class="gritty">
  <div class="gritty-item">
    <label class="label t-ellipsis d-b m-b-xs">
      &nbsp;
    </label>
    <input style="opacity: 0;" bind:value={lease_pm} type="number" disabled class="input-text w-62 t-r" />
    <div class="sum" style="opacity: 0;">&nbsp;</div>
    <div class="sum b-t-sub-sub" style="opacity: 0;">&nbsp;</div>

    <label class="label db" style="opacity: 0;"><input type="checkbox" disabled> Privé</label>
    <div class="sum-placeholder b-t-sub">zakelijk</div>
    <div class="sum-placeholder b-t-sub">prive</div>
  </div>

  <div class="gritty-item">
    <label class="label t-ellipsis d-b m-b-xs">
      Leasebedrag
    </label>
    <input bind:value={lease_pm} type="number" class="input-text w-62 t-r" />
    <div class="sum">{lease_py.toFixed(2)} <span class="sum__right">p/j</span></div>
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
    <div class="sum">{writeOff.toFixed(2)} <span class="sum__right">p/j</span></div>
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
    <div class="sum">{bijtelling.toFixed(2)} <span class="sum__right">p/j</span></div>
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
    <div class="sum">{12 * insurance} <span class="sum__right">p/j</span></div>
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
    <div class="sum">{12 * roadTax} <span class="sum__right">p/j</span></div>
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
    <div class="sum">{main_py.toFixed(2)} <span class="sum__right">p/j</span></div>
    <div class="sum b-t-sub-sub">{mainTotal.toFixed(2)}</div>

    <label class="label db"><input type="checkbox" bind:checked={main_py_private}/> Privé</label>
    <div class="sum b-t-sub">{mainTotalBusiness.toFixed(2)}</div>
    <div class="sum b-t-sub">{mainTotalPrivate.toFixed(2)}</div>
  </div>

  <div class="gritty-item">
    <label class="label t-ellipsis d-b m-b-xs">
      (Brand)stof
    </label>
    <input bind:value={fuelTotal} disabled="true" type="number" class="input-text w-62 t-r" />
    <div class="sum">{(fuelTotal).toFixed(2)} <span class="sum__right">p/j</span></div> 
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
    <div class="sum">{km_verg_py.toFixed(2)} <span class="sum__right">p/j</span></div>
    <div class="sum b-t-sub-sub">{(months * (km_verg_py / 12)).toFixed(2)}</div>
    <div class="sum b-t-sub">{km_verg_total.toFixed(2)}</div>
  </div>
</div>
<br/>
<br/>
<div>
  <div>Zakelijke kosten: € {totalBusiness.toFixed(2)} </div> <!-- TODO: verreken met omzetbelasting -->
  <!-- TODO: btw aftrek vanuit privé (main, fuel) -->
  <div>Privé kosten: € {totalPrivate.toFixed(2)}</div>
  <div>Privé kosten (bruto): € {totalPrivateBeforeTaxes.toFixed(2)}</div>
  <div>Km-vergoeding: € {bijtelling === 0 ? km_verg_total.toFixed(2) : 0}</div>
</div>
