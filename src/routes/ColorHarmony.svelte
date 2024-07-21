<script>
    import { onMount } from 'svelte';
    import chroma from 'chroma-js';
  
    let baseColor = '#3498db'; // Alapértelmezett szín
    let selectedHarmony = 'complementary'; // Alapértelmezett harmónia típusa
    let harmonyColors = [];
  
    // Színharmónia kalkulátor
    function calculateHarmony(base, type) {
      switch (type) {
        case 'complementary':
          return [base, chroma(base).set('hsl.h', '+180').css()];
        case 'analogous':
          return [base, chroma(base).set('hsl.h', '-30').css(), chroma(base).set('hsl.h', '+30').css()];
        case 'triadic':
          return [base, chroma(base).set('hsl.h', '+120').css(), chroma(base).set('hsl.h', '+240').css()];
        default:
          return [base];
      }
    }
  
    onMount(() => {
      harmonyColors = calculateHarmony(baseColor, selectedHarmony);
    });
  
    // Reagál a változásokra
    $: harmonyColors = calculateHarmony(baseColor, selectedHarmony);
  </script>
  
  <style>
    .color-sample {
      width: 50px;
      height: 50px;
      display: inline-block;
      margin: 5px;
    }
  </style>
  
  <!-- Felhasználói felület -->
  <div>
    <h1>Színharmónia Generátor</h1>
    <label for="baseColor">Kiinduló szín:</label>
    <input type="color" id="baseColor" bind:value={baseColor}>
  
    <label for="harmony">Színharmónia típusa:</label>
    <select id="harmony" bind:value={selectedHarmony}>
      <option value="complementary">Komplementer</option>
      <option value="analogous">Analog</option>
      <option value="triadic">Triadikus</option>
    </select>
  
    <div>
      {#each harmonyColors as color}
        <div class="color-sample" style="background-color: {color};"></div>
      {/each}
    </div>
  </div>
  