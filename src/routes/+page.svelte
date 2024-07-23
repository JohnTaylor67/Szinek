<script>
  import { onMount } from 'svelte';
  import chroma from 'chroma-js';

  let baseColor = '#3498db'; // Alapértelmezett szín
  let selectedHarmony = 'complementary'; // Alapértelmezett harmónia típusa
  let harmonyColors = [];
  let canvas, ctx; // Canvas és context referenciák

  // Színharmóniák kiszámítása
  function calculateHarmony(base, type) {
    switch (type) {
      case 'complementary':
        return [base, chroma(base).set('hsl.h', '+180').css()];
      case 'analogous':
        return [base, chroma(base).set('hsl.h', '-30').css(), chroma(base).set('hsl.h', '+30').css()];
      case 'triadic':
        return [base, chroma(base).set('hsl.h', '+120').css(), chroma(base).set('hsl.h', '+240').css()];
      case 'split-complementary':
        return [base, chroma(base).set('hsl.h', '+150').css(), chroma(base).set('hsl.h', '-150').css()];
      case 'square':
        return [base, chroma(base).set('hsl.h', '+90').css(), chroma(base).set('hsl.h', '+180').css(), chroma(base).set('hsl.h', '+270').css()];
      case 'tetradic':
        return [base, chroma(base).set('hsl.h', '+90').css(), chroma(base).set('hsl.h', '+180').css(), chroma(base).set('hsl.h', '+270').css()];
      default:
        return [base];
    }
  }

  onMount(() => {
    canvas = document.getElementById('colorWheel');
    if (canvas) {
      ctx = canvas.getContext('2d');
      drawColorWheel();
      harmonyColors = calculateHarmony(baseColor, selectedHarmony);
      highlightColors(harmonyColors);
    }
  });

  $: harmonyColors = calculateHarmony(baseColor, selectedHarmony);
 




  $: if (ctx && harmonyColors) {
    highlightColors(harmonyColors);
  }

  function drawColorWheel() {
    const centerX = canvas.width / 2;
    const centerY = canvas.height / 2;
    const radius = Math.min(centerX, centerY) * 0.8; // Például a sugár 40%-a a kisebbik középponti értéknek

    for (let angle = 0; angle < 360; angle++) {
        const startAngle = (angle - 1) * Math.PI / 180;
        const endAngle = angle * Math.PI / 180;
        ctx.beginPath();
        ctx.moveTo(centerX, centerY);
        ctx.arc(centerX, centerY, radius, startAngle, endAngle);
        ctx.closePath();
        ctx.fillStyle = `hsl(${angle}, 100%, 50%)`;
        ctx.fill();
    }
}


function highlightColors(colors) {
    const centerX = canvas.width / 2;
    const centerY = canvas.height / 2;
    const radius = Math.min(centerX, centerY) * 0.8; // Ugyanaz a sugár, mint a színkörhöz

    ctx.clearRect(0, 0, canvas.width, canvas.height);
    drawColorWheel();

    colors.forEach(color => {
        const hue = chroma(color).hsl()[0];
        const angle = hue * Math.PI / 180;
        ctx.beginPath();
        ctx.moveTo(centerX, centerY);
        ctx.arc(centerX, centerY, radius - 5, angle - 0.05, angle + 0.05); // Kis csökkentés a kiemeléshez
        ctx.closePath();
        ctx.fillStyle = color;
        ctx.fill();
    });
}

</script>



  
<style>
  :global(html, body) {
    height: 100%; /* Ez biztosítja, hogy a html és a body elem teljes magasságú legyen */
    margin: 0;
    padding: 0;
    overflow: hidden; /* Eltávolítja a görgetési lehetőséget */
    display: flex;
    flex-direction: column;
  }

  header {
    background-color: #e6e6e6;
    padding: 10px 20px;
    text-align: center;
  }

  .container {
    display: flex;
    flex: 1 1 auto; /* Növeli a flexibilitást, hogy kitöltse a maradék helyet */
    padding: 0;
    background-color: #f0f0f0;
    width: 100vw; /* Biztosítja, hogy teljes szélességű legyen */
    max-width: 100%; /* Megakadályozza a konténer szélességének túlcsordulását */
    overflow: hidden; /* Eltávolítja a görgetési lehetőséget */
  }

  .column {
    flex: 1;
    padding: 10px;
    display: flex;
    flex-direction: column;
    margin: 5px;
    background-color: #dcdcdc;
    border-radius: 8px;
    border: 1px solid #f0f0f0;
  }
  .color-samples {
    display: flex;
    flex-wrap: wrap;
    gap: 10px;
    justify-content: flex-start;
    flex: 1; 
    overflow-y: auto; 
  }

  .color-sample {
    width: 60px;
    height: 60px;
    border: 1px solid #eee;
    box-shadow: 0 2px 4px rgba(0,0,0,0.1);
    display: flex;
    justify-content: center;
    align-items: center;
  }

  .color-code {
    font-size: 12px;
    font-family: monospace;
    text-align: center;
    padding-top: 5px;
  }
  
  
  
</style>


  
  <header>
    <h1>Színharmónia Generátor</h1>
  </header>
  
  <div class="container">
    <div class="column">
      <label for="harmony">Színharmónia típusa:</label>
      <select id="harmony" bind:value={selectedHarmony}>
        <option value="complementary">Komplementer</option>
        <option value="analogous">Analog</option>
        <option value="triadic">Triadikus</option>
        <option value="split-complementary">Split Complementary</option>
        <option value="square">Square</option>
        <option value="tetradic">Tetradic</option>
      </select>
    </div>
  

  
    <div class="column">
      <label for="baseColor">Kiinduló szín:</label>
      <input type="color" id="baseColor" bind:value={baseColor}>
    </div>
    <div class="column">
      <canvas id="colorWheel" width="300" height="300" style="border:1px solid #ccc;"></canvas>
  
    </div>
  
    <div class="column color-samples">
      {#each harmonyColors as color}
        <div>
          <div class="color-sample" style="background-color: {color};"></div>
          <div class="color-code">{color}</div>
        </div>
      {/each}
    </div>


  </div>

