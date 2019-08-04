
<script>
  let privateCar = true;
  
  let taxPressure = 0.5;
  $: taxMult = (1 / taxPressure);

  let kms_py = 17500; 
  let kms_ratio_private = 0.8; 
  let kms_verg_private = 0.19; 
  
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
  .label-normal { text-transform: none; letter-spacing: 0; }
  
  .input-text { height: 26px; font-size: 14px; }
  .input-text:hover { box-shadow: 0 0 0 2px grey; }
  .input-text:focus { box-shadow: 0 0 0 2px blue; }
  .t-r { text-align: right; }
  .w-62 { width: 62px; }
  .w-62 { width: 62px; }
</style>

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
  <input type="range" min="0" max="150000" bind:value="{kms_py}"/>
</p>

<p>
  <label class="label">Privé kilometers ratio: <span class="label-normal">{(kms_ratio_private * 100).toFixed(0)} %</span></label><br />
  <input type="range" min="0" max="1" step="0.05" bind:value="{kms_ratio_private}"/>
</p>

<p>
  <label class="label">Privé kilometers verg: <span class="label-normal">€ {(kms_verg_private).toFixed(2)}</span></label><br />
  <input type="range" min="0" max="1" step="0.01" bind:value="{kms_verg_private}"/>
</p>


<div>
  Toyota Auris
</div>
<div class="gritty">
  <p class="gritty-item">
    <label class="label">Bijtelling</label>
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
      Wegenb.
    </label>
    <input bind:value="{roadTax}" type="number" class="input-text w-62 t-r">
    <input type="checkbox" bind:checked="{roadTaxPrivate}">
    <br/>
    <span>{ 12 * roadTax} p/j</span>
    <span>{ months * roadTax }</span>
    <strong>{ roadTaxTotal.toFixed(2) }</strong>
  </p>

  <p>
    <label class="label">Brandstof</label>
    <input bind:value="{fuel}" type="number" class="input-text w-62 t-r">
    <input type="checkbox" bind:checked="{fuelPrivate}">
    <br/>
    <strong>{ months * fuel }</strong>
  </p>

  <p>
    <label class="label">Onderhoud</label>
    <input bind:value="{main_py}" type="number" class="input-text w-62 t-r">
    <input type="checkbox" bind:checked={main_py_private}/><br/>
    <strong>{ months * (main_py / 12) }</strong>
  </p>

  <p>
    <label class="label">
      Kilometervergoeding
      <span style="color: #999">{(kmsVerg_py / 0.19).toFixed(2)} km/j</span>
    </label>
    <input bind:value="{kmsVerg_py}" type="number" class="input-text w-62 t-r"><br/>
    <strong>{ months * (kmsVerg_py / 12) } </strong>
  </p>

  <p>
    <label class="label">
      Afschrijving per jaar
    </label>
    <input bind:value="{writeOffPerYear}" type="number" class="input-text w-62 t-r"><br/>
    <strong>{ months * (writeOffPerYear / 12) }</strong>
  </p>

  <div>
  
    <hr />
    € {(totalPrivate * -1).toFixed(2)} Benodigd bruto inkomen
  </div>
</div>



