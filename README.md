
<!DOCTYPE html>
<html lang="bn">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>JAKARTIA CANDLE AI</title>
<style>
*{box-sizing:border-box}
body{margin:0;background:#080e19;color:#fff;font-family:Arial,sans-serif;padding:14px}
header{text-align:center;padding:18px;color:#20d9a0}
h1{font-size:24px}
.card{background:#151f30;border:1px solid #29394c;border-radius:13px;padding:15px;margin:14px 0}
h3{color:#20d9a0}
iframe{width:100%;height:420px;border:0;border-radius:8px}
input,select{width:100%;padding:12px;margin:6px 0;background:#080e19;color:white;border:1px solid #43536a;border-radius:7px;font-size:16px}
button{width:100%;padding:14px;background:#13a879;color:white;border:0;border-radius:8px;font-size:16px;font-weight:bold;margin-top:10px}
.result{padding:15px;background:#080e19;border-radius:9px;margin-top:12px;line-height:1.7}
.small{color:#9eabc0;font-size:13px;line-height:1.6}
.green{color:#21d99b}
.red{color:#ff6666}
label{display:block;margin-top:10px;color:#bdc9d9}
footer{text-align:center;color:#8190a5;font-size:12px;padding:20px}
</style>
</head>
<body>

<header>
<h1>📊 JAKARTIA CANDLE AI</h1>
<p>Smart Candlestick Analysis</p>
</header>

<div class="card">
<h3>📈 Live Market Chart</h3>
<select id="market" onchange="changeMarket()">
<option value="FX:USDMXN">USD / MXN</option>
<option value="FX:EURUSD">EUR / USD</option>
<option value="FX:GBPUSD">GBP / USD</option>
<option value="FX:USDJPY">USD / JPY</option>
<option value="FX:USDCHF">USD / CHF</option>
</select>
<iframe id="chart" src="https://s.tradingview.com/widgetembed/?symbol=FX%3AUSDMXN&interval=1&hidesidetoolbar=1&theme=dark&style=1&timezone=Etc%2FUTC"></iframe>
<p class="small">TradingView live chart. Market data availability depends on the provider.</p>
</div>

<div class="card">
<h3>🕯️ Candle Pattern Analyzer</h3>
<p class="small">একটি ক্যান্ডেলের OHLC মূল্য অথবা আগের ও বর্তমান দুটি ক্যান্ডেলের মূল্য বসাও।</p>

<label>Previous Candle Open</label>
<input id="po" type="number" step="any" placeholder="Previous Open">

<label>Previous Candle Close</label>
<input id="pc" type="number" step="any" placeholder="Previous Close">

<label>Current Candle Open</label>
<input id="o" type="number" step="any" placeholder="Current Open">

<label>Current Candle High</label>
<input id="h" type="number" step="any" placeholder="Current High">

<label>Current Candle Low</label>
<input id="l" type="number" step="any" placeholder="Current Low">

<label>Current Candle Close</label>
<input id="c" type="number" step="any" placeholder="Current Close">

<button onclick="analyze()">🔍 ANALYZE CANDLE</button>
<div class="result" id="result">
বিশ্লেষণের ফলাফল এখানে দেখা যাবে।
</div>
</div>

<div class="card">
<h3>📚 Pattern Library</h3>
<p>🟢 Hammer — সম্ভাব্য Bullish Reversal</p>
<p>🔴 Shooting Star — সম্ভাব্য Bearish Reversal</p>
<p>🟢 Bullish Engulfing — Bullish Signal</p>
<p>🔴 Bearish Engulfing — Bearish Signal</p>
<p>⚪ Doji — বাজারে অনিশ্চয়তা</p>
</div>

<div class="card">
<h3>⚠️ Risk Warning</h3>
<p class="small">এই Analyzer ইনপুট করা OHLC ডেটা বিশ্লেষণ করে। এটি স্বয়ংক্রিয়ভাবে TradingView থেকে OHLC সংগ্রহ করে না। কোনো সংকেত নিশ্চিত লাভের নিশ্চয়তা নয়।</p>
</div>

<footer>JAKARTIA CANDLE AI<br>Developed by JAKARTIA</footer>

<script>
function changeMarket(){
let s=document.getElementById("market").value;
document.getElementById("chart").src=
"https://s.tradingview.com/widgetembed/?symbol="+
encodeURIComponent(s)+"&interval=1&hidesidetoolbar=1&theme=dark&style=1&timezone=Etc%2FUTC";
}

function analyze(){
let po=Number(document.getElementById("po").value);
let pc=Number(document.getElementById("pc").value);
let o=Number(document.getElementById("o").value);
let h=Number(document.getElementById("h").value);
let l=Number(document.getElementById("l").value);
let c=Number(document.getElementById("c").value);
let r=document.getElementById("result");

let fields=["o","h","l","c"];
if(fields.some(id=>document.getElementById(id).value==="")){
r.innerHTML="⚠️ Current candle-এর সব OHLC মূল্য লিখুন।";
return;
}

if(![o,h,l,c].every(Number.isFinite)||h<Math.max(o,c)||l>Math.min(o,c)||h<l){
r.innerHTML="⚠️ OHLC মূল্য সঠিকভাবে দিন। High সর্বোচ্চ এবং Low সর্বনিম্ন হতে হবে।";
return;
}

let body=Math.abs(c-o);
let range=h-l;
let upper=h-Math.max(o,c);
let lower=Math.min(o,c)-l;
let pattern="সাধারণ ক্যান্ডেল";
let signal="NEUTRAL";
let detail="স্পষ্ট Reversal Pattern শনাক্ত হয়নি।";

if(range===0){
pattern="Invalid Candle";
detail="ক্যান্ডেলের High ও Low একই।";
}else if(body<=range*0.1){
pattern="Doji";
detail="বাজারে অনিশ্চয়তা বা ক্রেতা-বিক্রেতার ভারসাম্য।";
}else if(lower>=body*2&&upper<=body*0.5){
pattern="Hammer";
signal="POTENTIAL BULLISH";
detail="সম্ভাব্য Bullish Reversal। আগের Downtrend ও পরবর্তী Confirmation দরকার।";
}else if(upper>=body*2&&lower<=body*0.5){
pattern="Shooting Star";
signal="POTENTIAL BEARISH";
detail="সম্ভাব্য Bearish Reversal। আগের Uptrend ও Confirmation দরকার।";
}else if(c>o){
signal="BULLISH";
detail="ক্যান্ডেলটি Bullish। ক্রেতাদের চাপ বেশি ছিল।";
}else if(c<o){
signal="BEARISH";
detail="ক্যান্ডেলটি Bearish। বিক্রেতাদের চাপ বেশি ছিল।";
}

if(document.getElementById("po").value!==""&&document.getElementById("pc").value!==""){
if(!Number.isFinite(po)||!Number.isFinite(pc)||po===pc&&false){
r.innerHTML="⚠️ Previous candle-এর মূল্য যাচাই করুন।";
return;
}
if(pc<po&&c>o&&o<=pc&&c>=po){
pattern="Bullish Engulfing";
signal="POTENTIAL BULLISH";
detail="আগের Bearish candle-কে বর্তমান Bullish candle ঢেকে দিয়েছে।";
}
if(pc>po&&c<o&&o>=pc&&c<=po){
pattern="Bearish Engulfing";
signal="POTENTIAL BEARISH";
detail="আগের Bullish candle-কে বর্তমান Bearish candle ঢেকে দিয়েছে।";
}
}

let color=signal.includes("BULLISH")?"green":signal.includes("BEARISH")?"red":"";
r.innerHTML=
"<b>Pattern:</b> "+pattern+
"<br><b>Result:</b> <span class='"+color+"'>"+signal+"</span>"+
"<br><b>Analysis:</b> "+detail+
"<br><b>Candle Body:</b> "+body+
"<br><b>Upper Wick:</b> "+upper+
"<br><b>Lower Wick:</b> "+lower+
"<br><br>⚠️ এটি সম্ভাব্য প্যাটার্ন, নিশ্চিত ভবিষ্যদ্বাণী নয়।";
}
</script>
</body>
</html>
