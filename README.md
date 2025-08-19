<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Propzine – Real Estate ROI Calculator</title>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
  <script src="https://cdn.jsdelivr.net/npm/chart.js@4.4.1/dist/chart.umd.min.js"></script>
  <style>
    :root{
      --brand:#4A7CFF;         /* Propzine primary */
      --bg:#F8F9FA;            /* Light background */
      --text:#1E1E1E;          /* Dark text */
      --muted:#6B7280;         /* Gray-500 */
      --card: rgba(255,255,255,0.65);
      --blur: blur(10px);
      --radius: 18px;
    }
    *{box-sizing:border-box}
    body{margin:0;font-family:Inter,system-ui,-apple-system,Segoe UI,Roboto,Helvetica,Arial,sans-serif;background:var(--bg);color:var(--text)}
    .container{max-width:1100px;margin:40px auto;padding:16px}
    .title{font-weight:700;font-size:clamp(24px,3.2vw,36px);line-height:1.15;margin:0 0 6px}
    .subtitle{color:var(--muted);margin:0 0 24px}

    .grid{display:grid;grid-template-columns: 1fr 1fr;gap:22px}
    @media (max-width: 880px){.grid{grid-template-columns:1fr}}

    .card{background:var(--card);backdrop-filter:var(--blur);-webkit-backdrop-filter:var(--blur);border:1px solid rgba(255,255,255,0.6);box-shadow:0 10px 30px rgba(20,30,60,0.08);border-radius:var(--radius);}
    .card.pad{padding:18px}

    .input-row{display:grid;grid-template-columns:1fr 120px;gap:12px;margin-bottom:14px;align-items:center}
    .input-row.full{grid-template-columns:1fr}
    label{font-weight:600;font-size:14px;margin-bottom:6px;display:block}
    .field{display:flex;align-items:center;background:#fff;border:1px solid #e6e9ef;border-radius:14px;padding:10px 12px}
    .field input{width:100%;border:none;outline:none;background:transparent;font-size:15px}
    .prefix{color:var(--muted);margin-right:8px}
    .range{appearance:none;width:100%;height:8px;border-radius:999px;background:#e8ecf7;outline:none}
    .range::-webkit-slider-thumb{appearance:none;width:20px;height:20px;border-radius:50%;background:var(--brand);cursor:pointer;box-shadow:0 2px 6px rgba(24,39,75,0.15)}
    .pill{display:inline-flex;align-items:center;gap:10px;background:var(--brand);color:#fff;border:none;border-radius:999px;padding:12px 18px;font-weight:700;font-size:15px;cursor:pointer;box-shadow:0 8px 16px rgba(74,124,255,0.25);transition:transform .06s ease}
    .pill:active{transform:translateY(1px)}

    .kpis{display:grid;grid-template-columns:repeat(3,1fr);gap:14px}
    @media (max-width:880px){.kpis{grid-template-columns:1fr}}
    .kpi{padding:16px;border-radius:16px;background:linear-gradient(180deg, rgba(255,255,255,.85), rgba(255,255,255,.6));border:1px solid rgba(230,233,239,.9)}
    .kpi small{display:block;color:var(--muted);font-weight:600;margin-bottom:6px}
    .kpi .value{font-size:clamp(20px,3vw,28px);font-weight:800}

    .meta{display:flex;gap:10px;flex-wrap:wrap;margin-top:8px;color:var(--muted);font-size:13px}
    .badge{background:#eef3ff;color:#2a55ff;padding:6px 10px;border-radius:999px;border:1px dashed #bcd0ff}

    canvas{width:100%!important;height:420px!important}

    .footer-note{margin-top:10px;color:var(--muted);font-size:12px}
  </style>
</head>
<body>
  <div class="container">
    <h1 class="title">Real Estate ROI Calculator</h1>
    <p class="subtitle">Estimate appreciation + rental returns. Smooth, instant, and built for Indian investors.</p>

    <div class="grid">
      <!-- Inputs Card -->
      <section class="card pad" id="inputsCard">
        <div class="input-row">
          <div>
            <label for="purchase">Purchase Price</label>
            <div class="field"><span class="prefix">₹</span><input id="purchase" type="number" min="0" step="10000" value="5000000" placeholder="e.g., 50,00,000"/></div>
          </div>
        </div>

        <div class="input-row">
          <div>
            <label for="appreciation">Expected Annual Appreciation (%)</label>
            <input id="appreciation" class="range" type="range" min="0" max="15" step="0.1" value="7"/>
          </div>
          <div class="field"><input id="appreciationNum" type="number" min="0" max="15" step="0.1" value="7"/>%</div>
        </div>

        <div class="input-row">
          <div>
            <label for="rent">Monthly Rental Income</label>
            <div class="field"><span class="prefix">₹</span><input id="rent" type="number" min="0" step="1000" value="25000" placeholder="e.g., 25,000"/></div>
          </div>
          <div>
            <label for="costs">Annual Maint. + Taxes</label>
            <div class="field"><span class="prefix">₹</span><input id="costs" type="number" min="0" step="1000" value="60000" placeholder="e.g., 60,000"/></div>
          </div>
        </div>

        <div class="input-row">
          <div>
            <label for="years">Holding Period (Years)</label>
            <input id="years" class="range" type="range" min="1" max="30" value="10"/>
          </div>
          <div class="field"><input id="yearsNum" type="number" min="1" max="30" value="10"/></div>
        </div>

        <div class="input-row full">
          <button id="calcBtn" class="pill">Calculate Returns</button>
          <div class="meta">
            <span class="badge">INR • en-IN formatting</span>
            <span class="badge">Glass UI</span>
            <span class="badge">Chart.js</span>
          </div>
        </div>
      </section>

      <!-- Outputs Card -->
      <section class="card pad">
        <div class="kpis">
          <div class="kpi">
            <small>Final Property Value</small>
            <div class="value" id="finalValue">₹–</div>
          </div>
          <div class="kpi">
            <small>Total Net Rent Earned</small>
            <div class="value" id="netRent">₹–</div>
          </div>
          <div class="kpi">
            <small>Total ROI</small>
            <div class="value" id="roi">–%</div>
          </div>
        </div>
        <p class="footer-note" id="blurb"></p>
      </section>
    </div>

    <section class="card pad" style="margin-top:22px">
      <canvas id="roiChart" aria-label="ROI growth chart" role="img"></canvas>
      <p class="footer-note">Assumes simple model: ROI = (Future property value − Purchase price) + (Total rent − Total annual costs). Not investment advice.</p>
    </section>
  </div>

  <script>
    // Helpers
    const inr = new Intl.NumberFormat('en-IN', { style: 'currency', currency: 'INR', maximumFractionDigits: 0 });

    function animateNumber(el, from, to, duration=700){
      const start = performance.now();
      function step(now){
        const p = Math.min((now - start) / duration, 1);
        const val = from + (to - from) * p;
        el.textContent = el.id === 'roi' ? ${val.toFixed(1)}% : inr.format(val);
        if(p < 1) requestAnimationFrame(step);
      }
      requestAnimationFrame(step);
    }

    function calcSeries(purchase, appr, rent, costs, years){
      const seriesYears = [];
      const valueSeries = [];
      const totalSeries = [];
      let futureValue = purchase;
      let totalRent = 0;
      let totalCosts = 0;

      for(let y=1; y<=years; y++){
        futureValue = futureValue * (1 + appr/100);
        totalRent += (rent * 12);
        totalCosts += costs;
        const netRent = totalRent - totalCosts;
        seriesYears.push(Y${y});
        valueSeries.push(Math.round(futureValue));
        totalSeries.push(Math.round(futureValue + netRent));
      }

      const netRentTotal = totalRent - totalCosts;
      const roiAbs = (futureValue - purchase) + netRentTotal; // ₹
      const roiPct = (roiAbs / purchase) * 100; // % on initial capital

      return { seriesYears, valueSeries, totalSeries, futureValue, netRentTotal, roiAbs, roiPct };
    }

    // DOM refs
    const purchase = document.getElementById('purchase');
    const appreciation = document.getElementById('appreciation');
    const appreciationNum = document.getElementById('appreciationNum');
    const rent = document.getElementById('rent');
    const costs = document.getElementById('costs');
    const years = document.getElementById('years');
    const yearsNum = document.getElementById('yearsNum');

    const finalValue = document.getElementById('finalValue');
    const netRent = document.getElementById('netRent');
    const roi = document.getElementById('roi');
    const blurb = document.getElementById('blurb');
    const calcBtn = document.getElementById('calcBtn');

    // Sync range <-> number
    appreciation.addEventListener('input', () => appreciationNum.value = appreciation.value);
    appreciationNum.addEventListener('input', () => appreciation.value = appreciationNum.value);
    years.addEventListener('input', () => yearsNum.value = years.value);
    yearsNum.addEventListener('input', () => years.value = yearsNum.value);

    // Chart init
    const ctx = document.getElementById('roiChart').getContext('2d');
    let chart;
    function renderChart(labels, valueSeries, totalSeries){
      if(chart) chart.destroy();
      chart = new Chart(ctx, {
        type: 'line',
        data: {
          labels,
          datasets: [
            { label: 'Property Value', data: valueSeries, tension: 0.25, borderWidth: 3 },
            { label: 'Total Value (Incl. Net Rent)', data: totalSeries, tension: 0.25, borderWidth: 3 }
          ]
        },
        options: {
          responsive: true,
          maintainAspectRatio: false,
          plugins:{ legend:{ position:'bottom' }, tooltip:{ callbacks:{ label: ctx => ${ctx.dataset.label}: ${inr.format(ctx.parsed.y)} } } },
          scales:{ y:{ ticks:{ callback: v => inr.format(v) } } }
        }
      });
    }

    function run(){
      const P = Number(purchase.value||0);
      const A = Number(appreciation.value||0);
      const R = Number(rent.value||0);
      const C = Number(costs.value||0);
      const Y = Number(years.value||1);

      const { seriesYears, valueSeries, totalSeries, futureValue, netRentTotal, roiAbs, roiPct } = calcSeries(P, A, R, C, Y);

      animateNumber(finalValue, 0, Math.max(futureValue,0));
      animateNumber(netRent, 0, Math.max(netRentTotal,0));
      animateNumber(roi, 0, roiPct, 800);

      blurb.textContent = Over ${Y} year${Y>1?'s':''}, your property could grow to ${inr.format(futureValue)} with net rental income of ${inr.format(netRentTotal)}. Estimated total gain: ${inr.format(roiAbs)} (${roiPct.toFixed(1)}% of your initial investment).;
      renderChart(seriesYears, valueSeries, totalSeries);
    }

    calcBtn.addEventListener('click', run);

    // Auto-run once on load
    window.addEventListener('DOMContentLoaded', run);
  </script>
</body>
</html>
