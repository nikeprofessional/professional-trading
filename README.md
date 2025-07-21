<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>My SMC Trading Dashboard</title>
  <link href="https://cdn.jsdelivr.net/npm/tailwindcss@2.2.19/dist/tailwind.min.css" rel="stylesheet">
</head>
<body class="bg-gray-100 text-gray-800">
  <div class="container mx-auto p-6">
    <h1 class="text-3xl font-bold mb-4">📈 My SMC Trading Dashboard</h1>

    <div class="grid grid-cols-1 md:grid-cols-2 gap-6">
      <!-- GoCharting Embed -->
      <div>
        <h2 class="text-xl font-semibold mb-2">Live Gold Chart (GoCharting)</h2>
        <iframe src="https://www.gocharting.com/symbols/XAUUSD" width="100%" height="400" frameborder="0"></iframe>
      </div>

      <!-- Checklist Download -->
      <div>
        <h2 class="text-xl font-semibold mb-2">Daily Checklist</h2>
        <a href="assets/SMC_Daily_Checklist_XAUUSD.pdf" download class="inline-block px-4 py-2 bg-green-600 text-white rounded hover:bg-green-700">
          📥 Download Checklist PDF
        </a>
      </div>
    </div>

    <!-- Strategy Script Section -->
    <div class="mt-10">
      <h2 class="text-xl font-semibold mb-2">📜 SMC Strategy Script (GoCharting)</h2>
      <pre class="bg-gray-200 p-4 overflow-x-auto text-sm rounded"><code>
//@version=5
indicator("SMC Strategy for GoCharting", shorttitle="SMC-GC", overlay=true)

var lookback = input(20, "Structure Lookback")
var obCandleCount = input(8, "Order Block Width")

var hh = highest(high, lookback)
var ll = lowest(low, lookback)

var bosUp = high > hh[1] && low > low[1]
var bosDown = low < ll[1] && high < high[1]
var chochUp = low < ll[1] && high > high[1]
var chochDown = high > hh[1] && low < low[1]

if (bosUp) {
    plotshape("BOS Up", high, shape.labelup, color.green, "BOS 🔼")
}
if (bosDown) {
    plotshape("BOS Down", low, shape.labeldown, color.red, "BOS 🔽")
}
if (chochUp) {
    plotshape("CHoCH Up", low, shape.labelup, color.orange, "CHoCH 🔼")
}
if (chochDown) {
    plotshape("CHoCH Down", high, shape.labeldown, color.orange, "CHoCH 🔽")
}

var bullishOB = close[1] < open[1] && close > high[1]
var bearishOB = close[1] > open[1] && close < low[1]

if (bullishOB) {
    box("Bullish OB", bar_index - obCandleCount, low, bar_index, high, color.green, 70)
}
if (bearishOB) {
    box("Bearish OB", bar_index - obCandleCount, high, bar_index, low, color.red, 70)
}

if (high == hh) {
    hline("Liquidity High", hh, color.purple)
}
if (low == ll) {
    hline("Liquidity Low", ll, color.teal)
</code></pre>
    </div>

    <!-- Trade Journal -->
    <div class="mt-10">
      <h2 class="text-xl font-semibold mb-2">📘 Trade Journal</h2>
      <form id="journal-form" class="grid gap-2">
        <input type="text" id="pair" placeholder="Pair (e.g., XAUUSD)" class="p-2 border rounded">
        <input type="text" id="entry" placeholder="Entry Price" class="p-2 border rounded">
        <input type="text" id="exit" placeholder="Exit Price" class="p-2 border rounded">
        <input type="text" id="result" placeholder="Result (Win/Loss)" class="p-2 border rounded">
        <button type="submit" class="bg-blue-600 text-white px-4 py-2 rounded hover:bg-blue-700">Add Trade</button>
      </form>
      <div id="journal-entries" class="mt-4"></div>
    </div>
  </div>

<script>
  const form = document.getElementById('journal-form');
  const entriesDiv = document.getElementById('journal-entries');
  const trades = JSON.parse(localStorage.getItem('trades') || '[]');

  function updateEntries() {
    entriesDiv.innerHTML = trades.map(t => 
      `<div class='border-b py-2'><b>${t.pair}</b> | Entry: ${t.entry} | Exit: ${t.exit} | Result: ${t.result}</div>`
    ).join('');
  }

  form.onsubmit = function(e) {
    e.preventDefault();
    const trade = {
      pair: form.pair.value,
      entry: form.entry.value,
      exit: form.exit.value,
      result: form.result.value
    };
    trades.push(trade);
    localStorage.setItem('trades', JSON.stringify(trades));
    updateEntries();
    form.reset();
  }

  updateEntries();
</script>
</body>
</html>
# professional-trading
