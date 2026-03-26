<!DOCTYPE html>
<html lang="pt-br">
<head>
  <meta charset="UTF-8">
  <title>Sala de Sinais PRO</title>

<style>
body {
  background: #0f172a;
  color: white;
  font-family: Arial;
  text-align: center;
}

.grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 20px;
  padding: 20px;
}

.card {
  background: #1e293b;
  padding: 20px;
  border-radius: 15px;
}

.sinal {
  font-size: 28px;
}

.call { color: #00ff88; }
.put { color: #ff3b3b; }

.timer {
  font-size: 20px;
}
</style>

</head>

<body>

<h1>🔥 Sala de Sinais PRO</h1>

<div class="grid">

  <div class="card">
    <h3>EUR/USD M1</h3>
    <div id="eurusd_m1" class="sinal">...</div>
    <div id="timer_eurusd_m1" class="timer"></div>
  </div>

  <div class="card">
    <h3>GBP/USD M1</h3>
    <div id="gbpusd_m1" class="sinal">...</div>
    <div id="timer_gbpusd_m1" class="timer"></div>
  </div>

  <div class="card">
    <h3>EUR/JPY M1</h3>
    <div id="eurjpy_m1" class="sinal">...</div>
    <div id="timer_eurjpy_m1" class="timer"></div>
  </div>

  <div class="card">
    <h3>GBP/JPY M1</h3>
    <div id="gbpjpy_m1" class="sinal">...</div>
    <div id="timer_gbpjpy_m1" class="timer"></div>
  </div>

</div>

<script>
const API_KEY = "b7a8442395824a299190dc6f20845d6a";
let ultimos = {};

async function gerar(par, elemento, key) {
  const url = `https://api.twelvedata.com/time_series?symbol=${par}&interval=1min&outputsize=2&apikey=${API_KEY}`;

  const res = await fetch(url);
  const data = await res.json();

  if (!data.values) return;

  const ultimo = data.values[0];
  const anterior = data.values[1];

  const close = parseFloat(ultimo.close);
  const open = parseFloat(ultimo.open);

  let sinal = "AGUARDAR";

  const forca = Math.abs(close - open);

  if (close > anterior.close && forca > 0.0002) {
    sinal = "CALL";
    elemento.className = "sinal call";
  } else if (close < anterior.close && forca > 0.0002) {
    sinal = "PUT";
    elemento.className = "sinal put";
  }

  if (ultimos[key] !== sinal && sinal !== "AGUARDAR") {
    new Audio("https://www.soundjay.com/buttons/sounds/button-3.mp3").play();
    ultimos[key] = sinal;
  }

  elemento.innerText = sinal;
}

function rodar() {
  gerar("EUR/USD", document.getElementById("eurusd_m1"), "eurusd");
  gerar("GBP/USD", document.getElementById("gbpusd_m1"), "gbpusd");
  gerar("EUR/JPY", document.getElementById("eurjpy_m1"), "eurjpy");
  gerar("GBP/JPY", document.getElementById("gbpjpy_m1"), "gbpjpy");
}

setInterval(rodar, 60000);
rodar();
</script>

</body>
</html>
