<!doctype html>
<html lang="es">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>EnergyPro Plataforma</title>

<link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700&display=swap" rel="stylesheet">
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<script src="https://cdn.jsdelivr.net/npm/chartjs-plugin-zoom"></script>
<script src="https://cdn.jsdelivr.net/npm/jspdf"></script>

<style>
:root{
  --bg:#0b1220;
  --card:#111827;
  --accent:#22c55e;
  --accent2:#06b6d4;
  --text:#e5e7eb;
}

body.light{
  --bg:#eef4ff;
  --card:#ffffff;
  --text:#0f172a;
}

body{
  margin:0;
  font-family:Inter;
  background:linear-gradient(180deg,#020617,var(--bg));
  color:var(--text);
  transition:.4s;
}

/* HEADER */
.header{
  display:flex;
  justify-content:space-between;
  align-items:center;
  padding:25px 30px;
}

.title{
  font-size:40px;
  font-weight:700;
  animation:float 3s infinite;
}

@keyframes float{
  50%{transform:translateY(-5px)}
}

.container{max-width:1200px;margin:auto;padding:20px}

.grid{
  display:grid;
  grid-template-columns:1fr 250px;
  gap:20px;
}

.card{
  background:var(--card);
  padding:18px;
  border-radius:16px;
  margin-top:20px;
  transition:.4s;
}
.card:hover{transform:translateY(-5px)}

.btn{
  background:linear-gradient(135deg,var(--accent),var(--accent2));
  border:none;
  padding:10px;
  border-radius:10px;
  cursor:pointer;
  margin-top:8px;
}

.controls{
  margin-top:15px;
  display:flex;
  gap:10px;
  flex-wrap:wrap;
}
input{
  padding:8px;
  border-radius:8px;
  border:none;
  width:120px;
}

/* MEDIDOR */
.gauge{
  height:200px;
  border-radius:16px;
  background:linear-gradient(180deg,#111,#000);
  display:flex;
  flex-direction:column;
  justify-content:flex-end;
  overflow:hidden;
  position:relative;
}

.gauge-fill{
  width:100%;
  height:50%;
  background:linear-gradient(180deg,#22c55e,#06b6d4);
  transition:1s;
}

.gauge-label{
  position:absolute;
  top:10px;
  width:100%;
  text-align:center;
  font-size:14px;
}

/* IMÁGENES */
.image-grid{
  display:grid;
  grid-template-columns:1fr 1fr;
  gap:20px;
  margin-top:20px;
}

.image-grid img{
  width:100%;
  height:250px;
  object-fit:cover;
  border-radius:14px;
}

.compare{
  margin-top:40px;
  display:grid;
  grid-template-columns:1fr 1fr;
  gap:20px;
}
.compare div{
  background:#00000040;
  padding:15px;
  border-radius:10px;
}

/* INFO EXTRA */
.extra{
  margin-top:40px;
  line-height:1.8;
}
.extra h3{
  margin-top:25px;
  color:var(--accent);
}

</style>
</head>

<body class="light">

<div class="header">
  <button class="btn" onclick="toggleMode()">Modo</button>
  <div class="title">EnergyPro</div>
</div>

<div class="container">

<div class="grid">

<!-- IZQUIERDA -->
<div>

<div class="card">
<h2>Consumo Energético</h2>
<canvas id="chart"></canvas>

<div class="controls">
  <input type="number" id="newValue" placeholder="Nuevo valor">
  <button class="btn" onclick="addData()">Añadir</button>
  <button class="btn" onclick="resetData()">Reset</button>
  <button class="btn" onclick="downloadPDF()">PDF</button>
  <button class="btn" onclick="downloadImage()">PNG</button>
</div>

</div>

</div>

<!-- LATERAL DERECHO -->
<div>

<div class="card">
<h3>Medidor de Consumo</h3>
<div class="gauge">
  <div class="gauge-label" id="gaugeText">--</div>
  <div class="gauge-fill" id="gaugeFill"></div>
</div>
</div>

</div>

</div>

<!-- IMÁGENES -->
<div class="image-grid">
<img src="energy.jpeg">
<img src="https://images.unsplash.com/photo-1509395176047-4a66953fd231">
</div>

<!-- INFO ORIGINAL -->
<div class="card">
<h2>Información avanzada para empresas</h2>
<p>EnergyPro es una plataforma diseñada...</p>
</div>

<!-- EXTRA INFO -->
<div class="extra">

<h2>Análisis avanzado empresarial</h2>

<h3>Optimización energética</h3>
<p>Las empresas pueden reducir significativamente su consumo energético mediante la implementación de estrategias basadas en datos, Energy pro es una de ellas
  ayuda en la solucion de problemas, graficamente las empresas pueden ver sus costos y ventas relacioanndo la produccion ejecutada...</p>

<h3>Monitoreo inteligente</h3>
<p>El monitoreo en tiempo real permite detectar variaciones en el consumo y tomar decisiones inmediatas...</p>

<h3>Predicción de consumo</h3>
<p>Mediante el uso de análisis predictivo, es posible anticipar comportamientos futuros...</p>

<h3>Impacto financiero</h3>
<p>La reducción de costos energéticos impacta directamente en la rentabilidad empresarial...</p>

<h3>Sostenibilidad</h3>
<p>EnergyPro ayuda a cumplir con normativas ambientales...</p>

<h3>Transformación digital</h3>
<p>La digitalización energética permite a las empresas ser más competitivas...</p>

</div>

<!-- COMPARACIÓN -->
<div class="compare">
<div><h3>Método tradicional</h3><p>Monitoreo manual...</p></div>
<div><h3>EnergyPro</h3><p>Monitoreo en tiempo real...</p></div>
</div>

<div class="card">
<h2>Beneficios de EnergyPro</h2>
<ul>
<li>Reducción de costos</li>
<li>Eficiencia energética</li>
<li>Automatización</li>
</ul>
</div>

</div>

<script>

// DATA
let originalData=Array.from({length:12},()=>Math.random()*400+300);
let data=[...originalData];

// CHART
const chart=new Chart(document.getElementById('chart'),{
type:'line',
data:{
labels:data.map((_,i)=>i+1),
datasets:[{
data:data,
borderColor:'#22c55e',
backgroundColor:'rgba(34,197,94,0.2)',
tension:0.4,
fill:true
}]
}
});

// UPDATE
setInterval(()=>{
let val=Math.random()*400+300;
data.push(val);
data.shift();
updateChart(val);
},3000);

// UPDATE FUNC
function updateChart(val){
chart.data.datasets[0].data=data;
chart.update();

// ACTUALIZA MEDIDOR
let percent = (val/700)*100;
gaugeFill.style.height = percent+"%";
gaugeText.innerText = "Nivel: "+Math.round(percent)+"%";
}

// CONTROLES
function addData(){
let val=parseFloat(document.getElementById("newValue").value);
if(!isNaN(val)){
data.push(val);
data.shift();
updateChart(val);
}
}

function resetData(){
data=[...originalData];
updateChart(data[data.length-1]);
}

// EXPORT
function downloadPDF(){
const canvas=document.getElementById("chart");
const img=canvas.toDataURL("image/png");
const pdf=new jspdf.jsPDF();
pdf.text("Reporte EnergyPro",10,10);
pdf.addImage(img,"PNG",10,20,180,100);
pdf.save("grafica_energypro.pdf");
}

function downloadImage(){
const link=document.createElement("a");
link.download="grafica.png";
link.href=document.getElementById("chart").toDataURL();
link.click();
}

// MODO
function toggleMode(){
document.body.classList.toggle("light");
}

</script>

</body>
</html>
